# Concurrency params

`--maxConcurrency=<num>`
Prevents Jest from executing more than the specified amount of tests at the same time. Only affects tests that use test.concurrent.

`--maxWorkers=<num>|<string>`
Alias: -w. Specifies the maximum number of workers the worker-pool will spawn for running tests. In single run mode, this defaults to the number of the cores available on your machine minus one for the main thread. In watch mode, this defaults to half of the available cores on your machine to ensure Jest is unobtrusive and does not grind your machine to a halt. It may be useful to adjust this in resource limited environments like CIs but the defaults should be adequate for most use-cases.

For environments with variable CPUs available, you can use percentage based configuration: --maxWorkers=50%

`--runInBand`
Alias: -i. Run all tests serially in the current process, rather than creating a worker pool of child processes that run tests. This can be useful for debugging.

<!-- https://freecontent.manning.com/the-value-of-concurrency-in-tests/ -->
