# 0.1.1

# Improvements

* Use `compare_exchange_weak` for mutex acquisition because the acquisition is wrapped in a loop. (https://github.com/jbaublitz/per-thread-mutex/pull/4)
