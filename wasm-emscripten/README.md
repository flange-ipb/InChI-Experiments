# Build InChI to WebAssembly using Emscripten
`docker build .`

## Traps

### `__isascii`
The `__isascii` function used in *INCHI_BASE/src/util.c* needs to be provided by *ichicomp.h*. Fixed by *util.patch*. This problem was previously [reported](https://depth-first.com/articles/2019/05/15/compiling-inchi-to-webassembly-part-1/) in Richard Apodaca's Depth-First blog.

### Emscripten's file system
[Emscripten's file system architecture](https://emscripten.org/docs/porting/files/file_systems_overview.html#) is a bit different from what C programmers expect from *libc*. This becomes relevant when the *inchi-1.js* program (which runs in a node.js runtime) wants to interact with files in the test suite.

Solution: Addition of the linker flags `-lnodefs.js` and `-lnoderawfs.js` for the compilation of *INCHI_EXE*. Invocation of `NODEFS` is [well documented](https://emscripten.org/docs/api_reference/Filesystem-API.html#file-systems), but the article [does not mention `noderawfs.js` to be used for `NODERAWFS`](https://github.com/emscripten-core/emscripten/issues/15377#issuecomment-1285167486).

### Stack overflow in test *zzp-Polymers-FoldCRU-NPZZ*
Solution: Increase stack size using the linker flag `-sSTACK_SIZE` (default value is 65536; 1048576 is the lowest working value).
