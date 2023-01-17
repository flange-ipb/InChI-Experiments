FROM emscripten/emsdk:3.1.30

# Note: WORKDIR is /src
COPY inchify_*.sh test/
COPY util.patch .

RUN set -ex \
	&& apt-get update \
	&& apt-get -y install unzip dos2unix \
	&& git clone https://github.com/IUPAC-InChI/InChI_1_06.git \
#
# Apply patch to util.c
#
	&& cd InChI_1_06 \
	&& git apply ../util.patch \
#
# Compile INCHI_EXE
#
# creates inchi-1.js and inchi-1.wasm in /src/InChI_1_06/INCHI-1-SRC/INCHI_EXE/bin/wasm
#
	&& cd /src/InChI_1_06/INCHI-1-SRC/INCHI_EXE/inchi-1/gcc \
	&& mkdir -p ../../bin/wasm \
	&& make C_COMPILER=emcc CPP_COMPILER=em++ LINKER=em++ LINKER_OPTIONS="-lnodefs.js -lnoderawfs.js -sSTACK_SIZE=1048576" BIN_DIR=../../bin/wasm INCHI_EXECUTABLE_NAME=inchi-1.js \
#
# Prepare tests
#
	&& cd /src/InChI_1_06/INCHI-1-TEST/test \
	&& cp /src/test/inchify_*.sh . \
	&& unzip test-datasets.zip -d test-datasets \
	&& unzip test-results.zip -d expected-test-results \
	&& find expected-test-results/ -type f -exec dos2unix {} \; \
#
# Run tests
#
	&& ./inchify_InChI_TestSet.sh "node ../../INCHI-1-SRC/INCHI_EXE/bin/wasm/inchi-1.js" test-datasets test-results test-logs \
	&& ./inchify_zzp.sh "node ../../INCHI-1-SRC/INCHI_EXE/bin/wasm/inchi-1.js" test-datasets test-results test-logs \
#
# Compare test results with expected results
#
	&& diff -q test-results/ expected-test-results/ && echo "Tests are OK!"
