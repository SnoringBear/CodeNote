在 Go 语言中，使用 Redis 实现分布式锁可以通过 `github.com/go-redis/redis` 客户端库来实现。以下是完整的 Go 实现分布式锁的代码示例，包括获取锁、释放锁以及锁续期等功能。

------

### 1. 安装 Redis 客户端库

首先，安装 Go 的 Redis 客户端库：



```bash
go get github.com/go-redis/redis/v8
```

------

### 2. 实现分布式锁

以下是完整的 Go 实现分布式锁的代码：



```go
package main

import (
	"context"
	"fmt"
	"log"
	"time"

	"github.com/go-redis/redis/v8"
)

// RedisDistributedLock 分布式锁结构
type RedisDistributedLock struct {
	client    *redis.Client
	key       string
	value     string
	expire    time.Duration
	cancelCtx context.Context
	cancel    context.CancelFunc
}

// NewRedisDistributedLock 创建一个分布式锁实例
func NewRedisDistributedLock(client *redis.Client, key string, value string, expire time.Duration) *RedisDistributedLock {
	return &RedisDistributedLock{
		client: client,
		key:    key,
		value:  value,
		expire: expire,
	}
}

// Acquire 尝试获取锁
func (lock *RedisDistributedLock) Acquire(ctx context.Context) (bool, error) {
	// 使用 SET NX PX 命令获取锁
	result, err := lock.client.SetNX(ctx, lock.key, lock.value, lock.expire).Result()
	if err != nil {
		return false, err
	}
	if result {
		// 启动一个 goroutine 定期续期
		lock.cancelCtx, lock.cancel = context.WithCancel(ctx)
		go lock.renewLockPeriodically(lock.cancelCtx)
	}
	return result, nil
}

// Release 释放锁
func (lock *RedisDistributedLock) Release(ctx context.Context) (bool, error) {
	// 使用 Lua 脚本确保只有锁的持有者才能释放锁
	luaScript := `
	if redis.call("get", KEYS[1]) == ARGV[1] then
		return redis.call("del", KEYS[1])
	else
		return 0
	end
	`
	script := redis.NewScript(luaScript)
	result, err := script.Run(ctx, lock.client, []string{lock.key}, lock.value).Int64()
	if err != nil {
		return false, err
	}
	if result == 1 {
		// 停止续期 goroutine
		if lock.cancel != nil {
			lock.cancel()
		}
		return true, nil
	}
	return false, nil
}

// renewLockPeriodically 定期续期锁
func (lock *RedisDistributedLock) renewLockPeriodically(ctx context.Context) {
	ticker := time.NewTicker(lock.expire / 2) // 每隔一半的过期时间续期一次
	defer ticker.Stop()

	for {
		select {
		case <-ticker.C:
			// 续期锁
			luaScript := `
			if redis.call("get", KEYS[1]) == ARGV[1] then
				return redis.call("pexpire", KEYS[1], ARGV[2])
			else
				return 0
			end
			`
			script := redis.NewScript(luaScript)
			_, err := script.Run(ctx, lock.client, []string{lock.key}, lock.value, lock.expire.Milliseconds()).Result()
			if err != nil {
				log.Printf("Failed to renew lock: %v", err)
				return
			}
		case <-ctx.Done():
			// 上下文取消，停止续期
			return
		}
	}
}

func main() {
	// 创建 Redis 客户端
	client := redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "", // 无密码
		DB:       0,  // 默认数据库
	})

	// 测试 Redis 连接
	ctx := context.Background()
	pong, err := client.Ping(ctx).Result()
	if err != nil {
		log.Fatalf("Failed to connect to Redis: %v", err)
	}
	fmt.Println("Connected to Redis:", pong)

	// 创建分布式锁
	lockKey := "my_lock"
	lockValue := "unique_value_" + time.Now().String()
	expireTime := 10 * time.Second
	distLock := NewRedisDistributedLock(client, lockKey, lockValue, expireTime)

	// 尝试获取锁
	acquired, err := distLock.Acquire(ctx)
	if err != nil {
		log.Fatalf("Failed to acquire lock: %v", err)
	}
	if acquired {
		fmt.Println("Lock acquired, doing some work...")
		time.Sleep(5 * time.Second) // 模拟业务逻辑
		fmt.Println("Work done, releasing lock...")

		// 释放锁
		released, err := distLock.Release(ctx)
		if err != nil {
			log.Fatalf("Failed to release lock: %v", err)
		}
		if released {
			fmt.Println("Lock released")
		} else {
			fmt.Println("Failed to release lock")
		}
	} else {
		fmt.Println("Failed to acquire lock")
	}
}
```

------

### 3. 代码说明

1. **获取锁 (`Acquire`)**:
   - 使用 `SET NX PX` 命令尝试获取锁。
   - 如果获取成功，启动一个 goroutine 定期续期锁。
2. **释放锁 (`Release`)**:
   - 使用 Lua 脚本确保只有锁的持有者才能释放锁。
   - 释放锁后，停止续期 goroutine。
3. **锁续期 (`renewLockPeriodically`)**:
   - 每隔一半的锁过期时间，使用 Lua 脚本续期锁。
   - 如果锁已经丢失（例如被其他进程释放），则停止续期。
4. **Lua 脚本**:
   - Lua 脚本在 Redis 中执行是原子的，确保操作的原子性。

------

### 4. 运行示例

1. 启动 Redis 服务器。

2. 运行上述 Go 代码。

3. 如果成功获取锁，程序会输出：

   

   ```
   Lock acquired, doing some work...
   Work done, releasing lock...
   Lock released
   ```

4. 如果获取锁失败，程序会输出：

   

   ```
   Failed to acquire lock
   ```

------

### 5. 注意事项

1. **锁的唯一性**:
   - 锁的值 (`lockValue`) 必须是唯一的，通常可以使用 UUID 或时间戳。
2. **锁的过期时间**:
   - 过期时间应根据业务逻辑的执行时间合理设置。
3. **锁续期**:
   - 如果业务逻辑可能执行时间较长，建议启用锁续期机制。
4. **高可用性**:
   - 在生产环境中，建议使用 Redis 集群或 Redlock 算法来提高锁的可靠性。

------

通过以上实现，你可以在 Go 中使用 Redis 实现一个简单且可靠的分布式锁。