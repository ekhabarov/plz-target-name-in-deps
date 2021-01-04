# Issue descripton

## master branch: prober go_library target name == package name

### app/prober/BUILD

```python
go_library(
    name = "prober",
    srcs = ["prober.go"],
    visibility = ["//app/..."],
    import_path = "github.com/ekhabarov/plz-target-name-in-deps/app/prober",
)

```
### app/BUILD
```python
go_binary(
    name = "bin",
    srcs = [ "main.go" ],
    deps = [ "//app/prober:prober" ],
)
```

### Result
```shell
% plz clean
Cleaning in background; you may continue to do pleasing things in this repo in the meantime.

% plz build //app/prober:prober
Build finished; total time 10.47s, incrementality 0.0%. Outputs:
//app/prober:prober:
  plz-out/gen/app/prober/prober.a

% plz build //app:bin
Build finished; total time 550ms, incrementality 57.1%. Outputs:
//app:bin:
  plz-out/bin/app/bin

% file plz-out/bin/app/bin
plz-out/bin/app/bin: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, stripped
```

## lib-name branch: prboer go_library target name == "lib"

### Diff
```diff
diff --git a/app/BUILD b/app/BUILD
index 20a209b..ef3d916 100644
--- a/app/BUILD
+++ b/app/BUILD
@@ -1,6 +1,6 @@
 go_binary(
     name = "bin",
     srcs = [ "main.go" ],
-    deps = [ "//app/prober:prober" ],
+    deps = [ "//app/prober:lib" ],
 )

diff --git a/app/prober/BUILD b/app/prober/BUILD
index 770e102..d7e86f4 100644
--- a/app/prober/BUILD
+++ b/app/prober/BUILD
@@ -1,5 +1,5 @@
 go_library(
-    name = "prober",
+    name = "lib",
     srcs = ["prober.go"],
     visibility = ["//app/..."],
     import_path = "github.com/ekhabarov/plz-target-name-in-deps/app/prober"
```

### Result
```shell
% plz clean
Cleaning in background; you may continue to do pleasing things in this repo in the meantime.

% plz build //app/prober:lib
Build finished; total time 10.37s, incrementality 0.0%. Outputs:
//app/prober:lib:
  plz-out/gen/app/prober/lib.a

% plz build //app:bin
Build stopped after 200ms. 1 target failed:
    //app:_bin#lib
Error building target //app:_bin#lib: exit status 1
app/main.go, line 8, column 2: can't find import: "github.com/ekhabarov/plz-target-name-in-deps/app/prober"
```

### Expected result
```shell
% plz build //app:bin
Build finished; total time 550ms, incrementality 57.1%. Outputs:
//app:bin:
  plz-out/bin/app/bin
```
