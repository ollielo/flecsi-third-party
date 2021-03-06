FROM laristra/flecsi-buildenv:fedora

ARG SYSTEM_LIBS
ARG CMAKE_BUILD_TYPE
#Skip some tests
ARG CI
ARG TRAVIS
ARG TRAVIS_BRANCH

COPY flecsi-third-party/ /home/flecsi/tpl
COPY ccache/ /home/flecsi/.ccache
USER root
RUN chown -R flecsi:flecsi /home/flecsi/tpl /home/flecsi/.ccache /usr/local
USER flecsi

WORKDIR /home/flecsi/tpl
RUN mkdir -p build && cd build && \
    cmake -DBUILD_SHARED_LIBS=ON \
          -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} \
          -DUSE_SYSTEM_LIBS=${SYSTEM_LIBS} \
          .. && make -j2 && make clean;

USER root
RUN chown -R root:root /usr/local
USER flecsi

# Build FleCSI if it is not for DockerHub and master branch
WORKDIR /home/flecsi
RUN if [ ${TRAVIS_BRANCH} != refactor ]; then \
      git clone -b master --depth 1 --recursive https://github.com/laristra/flecsi flecsi && \
      cd flecsi && mkdir build && cd build && \
      cmake -DENABLE_UNIT_TESTS=ON -DFLECSI_RUNTIME_MODEL=legion -DENABLE_LEGION=ON .. && \
      make -j2 && make test && \
      cd ../.. && \
      rm -rf flecsi; \
    fi;
