## Use `EnumerableMap` for `Kernel.activePolicies`

An `EnumerableMap` like structure with mapping from `Policy=>bool` may be used for `Kernel.activePolicies` for preventing issues with duplicate prevention during development.