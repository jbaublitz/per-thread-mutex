# 0.1.3

# Maintenance

* Remove unused dependency from Cargo.toml. (https://github.com/jbaublitz/per-thread-mutex/pull/9)

# 0.1.2

# Improvements

* Only wake waiters on the mutex if the mutex is unlocked instead of each time a guard is dropped. This should be an optimization and improve
performance. (https://github.com/jbaublitz/per-thread-mutex/pull/6)

# 0.1.1

# Improvements

* Use `compare_exchange_weak` for mutex acquisition because the acquisition is wrapped in a loop. (https://github.com/jbaublitz/per-thread-mutex/pull/4)
