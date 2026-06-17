This is a minimal repro case for a bug involving rules_py and hermetic-launcher.

**How to repro**

1. Run the binary:
```bash
bazel run //:hello
```

It fails with:
```
INFO: Running command line: bazel-bin/hello
ERROR: execve failed with code -2
```

2. You can mitigate the regression by changing ruels_py_version in `MODULE.bazel` to the last commit before the change to hermetic-launcher:

```starlark
rules_py_version = "1f4c0414de6cc3b76045e52e9bf210b36f33367d"

archive_override(
    module_name = "aspect_rules_py",
    integrity = "sha256-zys5vqKP7eNZLR57ywWNdOjzaAKr9jPJVq+GB/PyjNU=",
    strip_prefix = "rules_py-{}".format(rules_py_version),
    urls = ["https://github.com/aspect-build/rules_py/archive/{}.zip".format(rules_py_version)],
)
```

Then run `bazel run //:hello`, which prints `hello`.
