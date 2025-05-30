
CLEANFILES = quantlib_wrap.cpp libQuantLibJNI.@JNILIB_EXTENSION@ QuantLib.jar

BUILT_SOURCES = quantlib_wrap.cpp quantlib_wrap.h

EXAMPLES = Bonds CallableBonds CallableBondsOAS DiscreteHedging EquityOptions FRA FunctionDelegates Time

SWIGJAVAFLAGS =

if JAVA_AUTOLOAD
SWIGJAVAFLAGS += -DJAVA_AUTOLOAD
endif

if JAVA_FINALIZER
SWIGJAVAFLAGS += -DJAVA_FINALIZER
endif

if JAVA_AUTOCLOSEABLE
SWIGJAVAFLAGS += -DJAVA_AUTOCLOSEABLE
endif

if HAVE_JAVA
if BUILD_JAVA

JAVAC_FLAGS ?= -O -g -source 8 -target 8

examples/%.class: examples/%.java QuantLib.jar
	$(JAVAC)  -cp QuantLib.jar ${JAVAC_FLAGS} examples/$*.java

.PHONY: $(EXAMPLES)

all-local: libQuantLibJNI.@JNILIB_EXTENSION@ QuantLib.jar

quantlib_wrap.o: quantlib_wrap.cpp quantlib_wrap.h
	$(CXX) -c quantlib_wrap.cpp -fno-strict-aliasing -fPIC $(CXXFLAGS) @JDK_INCLUDE@ @JDK_SYSTEM_INCLUDE@ `quantlib-config --cflags` -o quantlib_wrap.o

libQuantLibJNI.@JNILIB_EXTENSION@: quantlib_wrap.o
	$(CXX) $(CXXFLAGS) $(LDFLAGS) @SHARED_LIB@ quantlib_wrap.o -o libQuantLibJNI.@JNILIB_EXTENSION@ `quantlib-config --libs`

QuantLib.jar: quantlib_wrap.cpp org/quantlib/*.java
	mkdir -p bin
	find org/quantlib -name '*.java' | xargs $(JAVAC) ${JAVAC_FLAGS} -d bin
	$(JAR) cf QuantLib.jar -C bin org

install-exec-local:
	mkdir -p $(DESTDIR)/$(libdir)
	cp -p libQuantLibJNI.@JNILIB_EXTENSION@ $(DESTDIR)/$(libdir)/libQuantLibJNI.@JNILIB_EXTENSION@
	cp -p QuantLib.jar $(DESTDIR)/$(libdir)/QuantLib.jar

check-local: $(EXAMPLES)

$(EXAMPLES): $(EXAMPLES:%=examples/%.class)
	LD_LIBRARY_PATH=. $(JAVA) -cp ".:QuantLib.jar" examples.$@

endif
endif


quantlib_wrap.cpp: ../SWIG/*.i
	mkdir -p org/quantlib
	rm -f org/quantlib/*.java
	$(SWIG) $(SWIGFLAGS) $(SWIGJAVAFLAGS) -java -c++ -outdir org/quantlib \
            -package org.quantlib -o quantlib_wrap.cpp ../SWIG/quantlib.i

dist-hook:
	mkdir -p $(distdir)/org/quantlib
	cp ./org/quantlib/*.java $(distdir)/org/quantlib
	touch $(distdir)/quantlib_wrap.cpp
	touch $(distdir)/quantlib_wrap.h
	mkdir -p $(distdir)/examples
	cp ./examples/*.java $(distdir)/examples

clean-local:
	rm -rf org 
	rm -f examples/*.class
	rm -f quantlib_wrap.o
	rm -rf bin

EXTRA_DIST = README.txt $(BUILT_SOURCES)

