FROM buildpack-deps:stretch-scm

# gcc for cgo
RUN apt-get update && apt-get install -y --no-install-recommends \
		g++ \
		gcc \
		libc6-dev \
		make \
		pkg-config \
	&& rm -rf /var/lib/apt/lists/*

ENV GOLANG_VERSION 1.11.9

RUN set -eux; \
	\
# this "case" statement is generated via "update.sh"
	dpkgArch="$(dpkg --print-architecture)"; \
	case "${dpkgArch##*-}" in \
		amd64) goRelArch='linux-amd64'; goRelSha256='e88aa3e39104e3ba6a95a4e05629348b4a1ec82791fb3c941a493ca349730608' ;; \
		armhf) goRelArch='linux-armv6l'; goRelSha256='f0d7b039cae61efdc346669f3459460e3dc03b6c6de528ca107fc53970cba0d1' ;; \
		arm64) goRelArch='linux-arm64'; goRelSha256='892ab6c2510c4caa5905b3b1b6a1d4c6f04e384841fec50881ca2be7e8accf05' ;; \
		i386) goRelArch='linux-386'; goRelSha256='0fa4001fcf1ef0644e261bf6dde02fc9f10ae4df6d74fda61fc4d3c3cbef1d79' ;; \
		ppc64el) goRelArch='linux-ppc64le'; goRelSha256='6a0a6a80997529543a434667f404ead2da88ac8fecc59bfba82f62bd2588e6c8' ;; \
		s390x) goRelArch='linux-s390x'; goRelSha256='0dd7073469d0f414b264fbadc4f720f9582b13ff6a0a978a9ef23020f9e42ac1' ;; \
		*) goRelArch='src'; goRelSha256='ee80684b352f8d6b49d804d4e615f015ae92da41c4096cfee89ed4783b2498e3'; \
			echo >&2; echo >&2 "warning: current architecture ($dpkgArch) does not have a corresponding Go binary release; will be building from source"; echo >&2 ;; \
	esac; \
	\
	url="https://golang.org/dl/go${GOLANG_VERSION}.${goRelArch}.tar.gz"; \
	wget -O go.tgz "$url"; \
	echo "${goRelSha256} *go.tgz" | sha256sum -c -; \
	tar -C /usr/local -xzf go.tgz; \
	rm go.tgz; \
	\
	if [ "$goRelArch" = 'src' ]; then \
		echo >&2; \
		echo >&2 'error: UNIMPLEMENTED'; \
		echo >&2 'TODO install golang-any from jessie-backports for GOROOT_BOOTSTRAP (and uninstall after build)'; \
		echo >&2; \
		exit 1; \
	fi; \
	\
	export PATH="/usr/local/go/bin:$PATH"; \
	go version

ENV GOPATH /go
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH

RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH"
WORKDIR $GOPATH
