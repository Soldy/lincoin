include Makefile.include

AM_CPPFLAGS += -I$(top_srcdir)/src/leveldb/helpers/memenv \
  -I$(builddir)

noinst_LIBRARIES = liblincoin_server.a liblincoin_common.a liblincoin_cli.a

bin_PROGRAMS = lincoind lincoin-cli

SUBDIRS = . $(BUILD_QT) $(BUILD_TEST)
DIST_SUBDIRS = . qt test
.PHONY: FORCE
# lincoin core #
BITCOIN_CORE_H = addrman.h alert.h allocators.h base58.h bignum.h \
  rpcclient.h \
  rpcprotocol.h \
  rpcserver.h \
  bloom.h chainparams.h checkpoints.h checkqueue.h \
  clientversion.h coincontrol.h compat.h core.h coins.h crypter.h db.h hash.h init.h \
  key.h keystore.h leveldbwrapper.h limitedmap.h main.h miner.h mruset.h \
  netbase.h net.h noui.h protocol.h script.h serialize.h sync.h threadsafety.h \
  txdb.h txmempool.h ui_interface.h uint256.h util.h version.h walletdb.h wallet.h

JSON_H = json/json_spirit.h json/json_spirit_error_position.h \
  json/json_spirit_reader.h json/json_spirit_reader_template.h \
  json/json_spirit_stream_reader.h json/json_spirit_utils.h \
  json/json_spirit_value.h json/json_spirit_writer.h \
  json/json_spirit_writer_template.h

obj/build.h: FORCE
	@$(MKDIR_P) $(abs_top_builddir)/src/obj
	@$(top_srcdir)/share/genbuild.sh $(abs_top_builddir)/src/obj/build.h \
	  $(abs_top_srcdir)
version.o: obj/build.h

liblincoin_server_a_SOURCES = addrman.cpp alert.cpp \
  rpcserver.cpp \
  bloom.cpp \
  chainparams.cpp checkpoints.cpp coins.cpp crypter.cpp db.cpp \
  init.cpp keystore.cpp leveldbwrapper.cpp main.cpp miner.cpp \
  net.cpp noui.cpp rpcblockchain.cpp rpcdump.cpp \
  rpcmining.cpp rpcnet.cpp rpcrawtransaction.cpp rpcwallet.cpp \
  txdb.cpp txmempool.cpp wallet.cpp walletdb.cpp $(JSON_H) \
  $(BITCOIN_CORE_H)

liblincoin_common_a_SOURCES = \
  allocators.cpp \
  chainparams.cpp \
  core.cpp \
  hash.cpp \
  key.cpp \
  netbase.cpp \
  protocol.cpp \
  rpcprotocol.cpp \
  script.cpp \
  sync.cpp \
  util.cpp \
  version.cpp \
  $(BITCOIN_CORE_H)

liblincoin_cli_a_SOURCES = \
  rpcclient.cpp \
  $(BITCOIN_CORE_H)

nodist_liblincoin_common_a_SOURCES = $(top_srcdir)/src/obj/build.h
#

# lincoind binary #
lincoind_LDADD = liblincoin_server.a liblincoin_cli.a liblincoin_common.a leveldb/libleveldb.a leveldb/libmemenv.a \
  $(BOOST_LIBS)
lincoind_SOURCES = lincoind.cpp
#

if TARGET_WINDOWS
lincoind_SOURCES += lincoind-res.rc
endif

AM_CPPFLAGS += $(BDB_CPPFLAGS)
lincoind_LDADD += $(BDB_LIBS)

# lincoin-cli binary #
lincoin_cli_LDADD = liblincoin_cli.a liblincoin_common.a $(BOOST_LIBS)
lincoin_cli_SOURCES = lincoin-cli.cpp
#

if TARGET_WINDOWS
lincoin_cli_SOURCES += lincoin-cli-res.rc
endif

leveldb/libleveldb.a: leveldb/libmemenv.a

leveldb/%.a:
	@echo "Building LevelDB ..." && $(MAKE) -C $(@D) $(@F) CXX="$(CXX)" \
	  CC="$(CC)" PLATFORM=$(TARGET_OS) AR="$(AR)" $(LEVELDB_TARGET_FLAGS) \
	  OPT="$(CXXFLAGS) $(CPPFLAGS)"

qt/lincoinstrings.cpp: $(liblincoin_server_a_SOURCES) $(liblincoin_common_a_SOURCES) $(liblincoin_cli_a_SOURCES)
	@test -n $(XGETTEXT) || echo "xgettext is required for updating translations"
	@cd $(top_srcdir); XGETTEXT=$(XGETTEXT) share/qt/extract_strings_qt.py

CLEANFILES = leveldb/libleveldb.a leveldb/libmemenv.a *.gcda *.gcno

DISTCLEANFILES = obj/build.h

EXTRA_DIST = leveldb Makefile.include

clean-local:
	-$(MAKE) -C leveldb clean
	rm -f leveldb/port/*.gcno leveldb/db/*.gcno leveldb/table/*.gcno leveldb/helpers/*.gcno
	rm -f leveldb/util/*.gcno leveldb/helpers/memenv/*.gcno
