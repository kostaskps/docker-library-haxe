#
# NOTE: THIS DOCKERFILE IS GENERATED VIA "haxe update.hxml"
#
# PLEASE DO NOT EDIT IT DIRECTLY.
#

FROM buildpack-deps:stretch-scm

# ensure local haxe is preferred over distribution haxe
ENV PATH /usr/local/bin:$PATH

# runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
		libgc1c2 \
		zlib1g \
		libpcre3 \
		libmariadb2 \
		libsqlite3-0 \
		libmbedcrypto0 \
		libmbedtls10 \
		libmbedx509-0 \
	&& rm -rf /var/lib/apt/lists/*

# install neko, which is a dependency of haxelib
ENV NEKO_VERSION 2.3.0
RUN set -ex \
	&& buildDeps=' \
		gcc \
		make \
		cmake \
		libgc-dev \
		libssl-dev \
		libpcre3-dev \
		zlib1g-dev \
		apache2-dev \
		libmariadb-client-lgpl-dev-compat \
		libsqlite3-dev \
		libmbedtls-dev \
		libgtk2.0-dev \
	' \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends && rm -rf /var/lib/apt/lists/* \
	\
	&& wget -O neko.tar.gz "https://github.com/HaxeFoundation/neko/archive/v2-3-0/neko-2.3.0.tar.gz" \
	&& echo "850e7e317bdaf24ed652efeff89c1cb21380ca19f20e68a296c84f6bad4ee995 *neko.tar.gz" | sha256sum -c - \
	&& mkdir -p /usr/src/neko \
	&& tar -xC /usr/src/neko --strip-components=1 -f neko.tar.gz \
	&& rm neko.tar.gz \
	&& cd /usr/src/neko \
	&& cmake -DRELOCATABLE=OFF . \
	&& make \
	&& make install \
	\
	&& apt-get purge -y --auto-remove $buildDeps \
	&& rm -rf /usr/src/neko ~/.cache

# install haxe
ENV HAXE_VERSION 4.1.1
ENV HAXE_STD_PATH /usr/local/share/haxe/std
RUN set -ex \
	&& buildDeps=' \
		make \
		ocaml-nox \
		ocaml-native-compilers \
		ocaml-findlib \
		zlib1g-dev \
		libpcre3-dev \
		libmbedtls-dev \
		\
		gcc \
		g++ \
		pkg-config \
		m4 \
		unzip \
		patch \
		rsync \
		\
	' \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends \
	&& git clone --recursive --depth 1 --branch 4.1.1 "https://github.com/HaxeFoundation/haxe.git" /usr/src/haxe \
	&& mkdir -p $HAXE_STD_PATH \
	&& cp -r /usr/src/haxe/std/* $HAXE_STD_PATH \
	\
	\
	&& curl -sL https://github.com/ocaml/opam/releases/download/2.0.5/opam-full-2.0.5.tar.gz -o opam.tar.gz \
	&& echo "776c7e64d6e24c2ef1efd1e6a71d36e007645efae94eaf860c05c1929effc76f *opam.tar.gz" | sha256sum -c - \
	&& mkdir -p /usr/src/opam \
	&& tar -xC /usr/src/opam --strip-components=1 -f opam.tar.gz \
	&& rm opam.tar.gz \
	&& cd /usr/src/opam \
	&& ./configure --with-mccs \
	&& make -j1 lib-ext all install \
	&& opam init --disable-sandboxing \
	&& eval $(opam env) \
	&& opam install /usr/src/haxe --deps-only --yes \
	&& cd /usr/src/haxe \
	&& make \
	&& eval $(opam env --revert) \
	&& cd /usr/src/opam \
	&& make uninstall \
	&& cd /usr/src/haxe \
	\
	&& mkdir -p /usr/local/bin \
	&& cp haxe haxelib /usr/local/bin \
	&& mkdir -p /haxelib \
	&& cd / && haxelib setup /haxelib \
	\
	\
	&& rm -rf ~/.opam \
	&& rm -rf /usr/src/opam \
	\
	&& rm -rf /var/lib/apt/lists/* \
	&& apt-get purge -y --auto-remove $buildDeps \
	&& rm -rf /usr/src/haxe ~/.cache

CMD ["haxe"]
