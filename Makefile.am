EXTRA_DIST = COPYING scripts/findstatic.pl swupd.bash

AM_CFLAGS = -fPIC -Iinclude/ -O2 -g -Wall -W -Wformat-security -D_FORTIFY_SOURCE=2 -fno-common -std=gnu99
ACLOCAL_AMFLAGS = -I m4

bin_PROGRAMS = swupd

swupd_SOURCES = \
	src/swupd.c \
	$(swupd_hashdump_SOURCES) \
	$(swupd_update_SOURCES) \
	$(swupd_check_update_SOURCES) \
	$(swupd_verify_SOURCES) \
	$(clr_bundle_add_SOURCES) \
	$(clr_bundle_rm_SOURCES) \
	$(clr_bundle_ls_SOURCES) \
	$(swupd_search_SOURCES)

check_PROGRAMS = \
	swupd_bsdiff_bench \
	swupd_hashtest \
	swupd_listtest \
	swupd_fuzz \
	swupd_locktest \
	swupd_sig_verifytest

SWUPD_COMMON_SOURCES = \
	src/curl.c \
	src/delta.c \
	src/download.c \
	src/filedesc.c \
	src/hash.c \
	src/helpers.c \
	src/heuristics.c \
	src/list.c \
	src/lock.c \
	src/manifest.c \
	src/packs.c \
	src/signature.c \
	src/staging.c \
	src/stats.c \
	src/subscriptions.c \
	src/update.c \
	src/version.c \
	src/xattrs.c \
	src/globals.c \
	src/scripts.c \
	src/bundle.c \
	src/telemetry.c

lib_LTLIBRARIES = libswupd.la
libswupd_la_SOURCES = $(SWUPD_COMMON_SOURCES)
libswupd_la_LIBADD = $(SWUPD_COMPRESSION_LIBS) $(openssl_LIBS) $(curl_LIBS) $(bsdiff_LIBS)
# Library version changes according to the libtool convention:
# http://www.gnu.org/software/libtool/manual/libtool.html#Updating-version-info
LIBSWUPD_CURRENT=2
LIBSWUPD_REVISION=0
LIBSWUPD_AGE=0
libswupd_la_LDFLAGS = \
	-version-info $(LIBSWUPD_CURRENT):$(LIBSWUPD_REVISION):$(LIBSWUPD_AGE)

swupd_update_SOURCES = src/main.c
swupd_verify_SOURCES = src/verify.c
swupd_check_update_SOURCES = src/check_update.c
swupd_search_SOURCES = src/search.c

clr_bundle_add_SOURCES = src/clr_bundle_add.c
clr_bundle_rm_SOURCES = src/clr_bundle_rm.c
clr_bundle_ls_SOURCES = src/clr_bundle_ls.c

swupd_hashdump_SOURCES = src/hashdump.c

swupd_bsdiff_bench_SOURCES = test/bsdiff_bench.c
swupd_hashtest_SOURCES = test/hash_test.c
swupd_listtest_SOURCES = test/listtest.c
swupd_fuzz_SOURCES = test/fuzz.c
swupd_locktest_SOURCES = test/locktest.c
swupd_sig_verifytest_SOURCES = test/signature_verify_test.c


AM_CPPFLAGS = $(AM_CFLAGS) -I$(top_srcdir)/include
SWUPD_COMPRESSION_LIBS = $(lzma_LIBS) $(zlib_LIBS) $(bzip2_LIBS)
SWUPD_CORE_LIBS = libswupd.la ${curl_LIBS} $(openssl_LIBS) $(SWUPD_COMPRESSION_LIBS) $(bsdiff_LIBS)

swupd_LDADD = $(SWUPD_CORE_LIBS) $(pthread_LIBS)

swupd_bsdiff_bench_LDADD = $(SWUPD_CORE_LIBS)
swupd_hashtest_LDADD = $(SWUPD_CORE_LIBS) $(pthread_LIBS)
swupd_listtest_LDADD = $(SWUPD_CORE_LIBS)
swupd_fuzz_LDADD = $(SWUPD_CORE_LIBS) $(pthread_LIBS)
swupd_locktest_LDADD = $(SWUPD_CORE_LIBS) $(pthread_LIBS)
swupd_sig_verifytest_LDADD = $(SWUPD_CORE_LIBS)

noinst_HEADERS = $(top_srcdir)/include/*

EXTRA_DIST += \
	data/check-update.service \
	data/check-update.timer \
	data/swupd-update.service \
	data/swupd-update.timer

DISTCHECK_CONFIGURE_FLAGS = \
	--with-systemdsystemunitdir=$$dc_install_base/$(systemdunitdir)

systemdunitdir = @SYSTEMD_UNITDIR@

systemdunit_DATA = \
	data/check-update.service \
	data/check-update.timer \
	data/swupd-update.service \
	data/swupd-update.timer

distclean-local:
	rm -rf aclocal.m4 ar-lib autom4te.cache config.guess config.h.in config.h.in~ config.sub configure depcomp install-sh ltmain.sh m4 Makefile.in missing compile

install-exec-hook:
	perl $(top_srcdir)/scripts/findstatic.pl */*.o | grep -v Checking ||:


TEST_EXTENSIONS = .bats .prereq

if ENABLE_TESTS
tap_driver = env AM_TAP_AWK='$(AWK)' $(SHELL) \
	$(top_srcdir)/tap-driver.sh

LOG_DRIVER = $(tap_driver)
BATS_LOG_DRIVER = $(tap_driver)

TESTS = $(dist_check_SCRIPTS)

dist_check_SCRIPTS = \
	test/functional/bundleadd/add-directory/test.bats \
	test/functional/bundleadd/add-existing/test.bats \
	test/functional/bundleadd/add-multiple/test.bats \
	test/functional/bundleadd/boot-file/test.bats \
	test/functional/bundleadd/include/test.bats \
	test/functional/bundleadd/fix-missing-file/test.bats \
	test/functional/bundleadd/verify-fix-path/test.bats \
	test/functional/bundlelist/all/test.bats \
	test/functional/bundleremove/boot-file/test.bats \
	test/functional/bundleremove/include/test.bats \
	test/functional/bundleremove/parse-args/test.bats \
	test/functional/bundleremove/remove-file/test.bats \
	test/functional/checkupdate/new-version/test.bats \
	test/functional/checkupdate/no-server-content/test.bats \
	test/functional/checkupdate/no-target-content/test.bats \
	test/functional/checkupdate/slow-server/test.bats \
	test/functional/checkupdate/version-match/test.bats \
	test/functional/hashdump/file-hash/test.bats \
	test/functional/hashdump/file-hash-no-path-prefix/test.bats \
	test/functional/search/content-check-negfull-path/test.bats \
	test/functional/search/content-check-neglibtest/test.bats \
	test/functional/search/content-check-posbin/test.bats \
	test/functional/search/content-check-posebin/test.bats \
	test/functional/search/content-check-posfull-path/test.bats \
	test/functional/search/content-check-poslib32/test.bats \
	test/functional/search/content-check-poslib64/test.bats \
	test/functional/update/apply-full-file-delta/test.bats \
	test/functional/update/boot-file/test.bats \
	test/functional/update/download/test.bats \
	test/functional/update/include/test.bats \
	test/functional/update/include-old-bundle/test.bats \
	test/functional/update/include-old-bundle-with-tracked-file/test.bats \
	test/functional/update/missing-os-core/test.bats \
	test/functional/update/newest-deleted/test.bats \
	test/functional/update/skip-verified-fullfiles/test.bats \
	test/functional/update/status/test.bats \
	test/functional/update/status-no-server-content/test.bats \
	test/functional/update/status-no-target-content/test.bats \
	test/functional/update/status-version-double-quote/test.bats \
	test/functional/update/status-version-single-quote/test.bats \
	test/functional/update/use-full-file/test.bats \
	test/functional/update/use-pack/test.bats \
	test/functional/update/verify-fix-path-hash-mismatch/test.bats \
	test/functional/update/verify-fix-path-missing-dir/test.bats \
	test/functional/update/verify-fullfile-hash/test.bats \
	test/functional/verify/add-missing-directory/test.bats \
	test/functional/verify/add-missing-include/test.bats \
	test/functional/verify/add-missing-include-old/test.bats \
	test/functional/verify/boot-file-deleted/test.bats \
	test/functional/verify/boot-file-mismatch/test.bats \
	test/functional/verify/boot-file-mismatch-fix/test.bats \
	test/functional/verify/check-missing-directory/test.bats \
	test/functional/verify/directory-tree-deleted/test.bats \
	test/functional/verify/empty-dir-deleted/test.bats \
	test/functional/verify/install-directory/test.bats \
	test/functional/verify/install-latest-directory/test.bats \
	test/functional/verify/latest-missing/test.bats

# Must be run before all other tests
dist_check_SCRIPTS += test/functional/generate-cert.prereq

parallel_tests := $(patsubst %.bats,%.log,$(filter %.bats,$(dist_check_SCRIPTS)))
$(parallel_tests) : test/functional/generate-cert.log
endif



if COVERAGE
AM_CFLAGS += --coverage

coverage: coverage-clean
	mkdir -p coverage
	lcov --compat-libtool --directory . --capture --output-file coverage/report
	genhtml -o coverage/ coverage/report

coverage-clean:
	rm -rf coverage
endif

compliant:
	@git diff --quiet --exit-code include src; ret=$$?; \
	if [ $$ret -eq 1 ]; then \
		echo "Error: can only check code style when include/ and src/ are clean."; \
		echo "Stash or commit your changes and try again."; \
		exit $$ret; \
	elif [ $$ret -gt 1 ]; then \
		exit $$ret; \
	fi; \
	clang-format -i -style=file include/*.h src/*.c; ret=$$?; \
	if [ $$ret -ne 0 ]; then \
		exit $$ret; \
	fi; \
	git diff --quiet --exit-code include src; ret=$$?; \
	if [ $$ret -eq 1 ]; then \
		echo "Code style issues found. Run 'git diff' to view issues."; \
	elif [ $$ret -eq 0 ]; then \
		echo "No code style issues found."; \
	fi; \
	exit $$ret

release:
	@git rev-parse v$(PACKAGE_VERSION) &> /dev/null; \
	if [ "$$?" -eq 0 ]; then \
		echo "Error: Release for $(PACKAGE_VERSION) already exists."; \
		echo "Bump version in configure.ac before releasing."; \
		exit 1; \
	fi
	@git tag -a -m "$(PACKAGE_NAME) release $(PACKAGE_VERSION)" v$(PACKAGE_VERSION)
	@printf "\nNew release $(PACKAGE_VERSION) tagged!\n\n"


MANPAGES = \
	docs/swupd.1 \
	docs/check-update.service.4 \
	docs/check-update.timer.4 \
	docs/swupd-update.service.4 \
	docs/swupd-update.timer.4 \
	docs/update-triggers.target.4

manpages:
	for MANPAGE in $(MANPAGES); do \
		ronn --roff < $${MANPAGE}.md > $${MANPAGE}; \
		ronn --html < $${MANPAGE}.md > $${MANPAGE}.html; \
	done

dist_man_MANS = \
	$(MANPAGES)
