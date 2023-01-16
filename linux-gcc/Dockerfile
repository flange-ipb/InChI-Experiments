FROM debian:stable

COPY inchify_*.sh /test/

RUN set -ex \
	&& apt-get update \
	&& apt-get -y install git build-essential unzip dos2unix \
	&& git clone https://github.com/IUPAC-InChI/InChI_1_06.git \
#
# Compile INCHI_EXE
#
# creates the executeable /InChI_1_06/INCHI-1-SRC/INCHI_EXE/bin/Linux/inchi-1
#
	&& cd /InChI_1_06/INCHI-1-SRC/INCHI_EXE/inchi-1/gcc \
	&& make -j \
#
# Prepare tests
#
	&& cd /InChI_1_06/INCHI-1-TEST/test \
	&& cp /test/inchify_*.sh . \
	&& unzip test-datasets.zip -d test-datasets \
	&& unzip test-results.zip -d expected-test-results \
	&& find expected-test-results/ -type f -exec dos2unix {} \; \
#
# Run tests
#
	&& ./inchify_InChI_TestSet.sh ../../INCHI-1-SRC/INCHI_EXE/bin/Linux/inchi-1 test-datasets test-results test-logs \
	&& ./inchify_zzp.sh ../../INCHI-1-SRC/INCHI_EXE/bin/Linux/inchi-1 test-datasets test-results test-logs \
#
# Compare test results with expected results
#
	&& diff -q test-results/ expected-test-results/ && echo "Tests are OK!"
