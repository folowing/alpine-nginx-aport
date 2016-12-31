# Maintainer: Rocky Feng <folowing@gmail.com>

pkgname=nginx
pkgver=1.11.8
pkgrel=0
pkgdesc="HTTP and reverse proxy server"
url="http://www.nginx.org/en"
arch="all"
license="custom"

# Modules
depends=""
makedepends="linux-headers libressl-dev pcre-dev pkgconf zlib-dev"
pkgusers="nginx"
pkggroups="nginx"
install="nginx.pre-install nginx.post-upgrade"
subpackages=""
replaces="nginx-common nginx-initscripts"
source="http://nginx.org/download/nginx-$pkgver.tar.gz
        nginx.conf
        default.conf
        "
builddir="$srcdir/nginx-$pkgver"

_modules_dir="usr/lib/nginx/modules"


build() {
    cd "$builddir"

    ./configure \
        --prefix=/var/lib/nginx \
        --sbin-path=/usr/sbin/nginx \
        --modules-path=/$_modules_dir \
        --conf-path=/etc/nginx/nginx.conf \
        --error-log-path=/var/log/nginx/error.log \
        --http-log-path=/var/log/nginx/access.log \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/run/nginx.lock \
        --http-client-body-temp-path=/var/cache/nginx/client_temp \
        --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
        --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
        --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
        --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
        --user=$pkgusers \
        --group=$pkggroups \
        --with-http_ssl_module \
        --with-http_realip_module \
        --with-http_addition_module \
        --with-http_sub_module \
        --with-http_dav_module \
        --with-http_flv_module \
        --with-http_mp4_module \
        --with-http_gunzip_module \
        --with-http_gzip_static_module \
        --with-http_random_index_module \
        --with-http_secure_link_module \
        --with-http_slice_module \
        --with-http_stub_status_module \
        --with-http_auth_request_module \
        --with-threads \
        --with-stream \
        --with-stream_ssl_module \
        --with-stream_ssl_preread_module \
        --with-stream_realip_module \
        --with-compat \
        --with-file-aio \
        --with-http_v2_module \
        || return 1

    make || return 1
}

package() {
    cd "$builddir"

    make DESTDIR="$pkgdir" install || return 1

    cd "$pkgdir"

    install -Dm644 "$srcdir"/nginx.conf ./etc/nginx/nginx.conf
    install -Dm644 "$srcdir"/default.conf ./etc/nginx/conf.d/default.conf

    install -dm755 -o $pkgusers -g $pkggroups ./var/lib/nginx
    install -dm700 -o $pkgusers -g $pkggroups ./var/cache/nginx
    install -dm755 -o $pkgusers -g $pkggroups ./var/log/nginx
    install -dm755 -o $pkgusers -g $pkggroups ./$_modules_dir/

    ln -sf /$_modules_dir ./etc/nginx/modules
}


md5sums="8f68f49b6db510e567bba9e0c271a3ac  nginx-1.11.8.tar.gz
256145c0f70d1d1d3b99f854553d48f0  nginx.conf
3d26969d4e77e0bc222ad9b32964f6f7  default.conf
sha256sums="53aef3715d79015314c2dcb18f2b185a0c64368cc01b30bdf0737a215f666b34  nginx-1.11.8.tar.gz
df873f301f947192c854994bb0e1bac46f73a5d3cf91df997f1b6a8ed26b5724  nginx.conf
c6b27adac2153ac5ca92aa0bcd933657bf8e17d88d7b1ab962768dd9fc6f310b  default.conf
sha512sums="4bbecf17579022cc925af8808554983c57e4f438edc8f987751413f0a023267a4766edc8321cbbe8a8b675f7e86d8a2cba76bd52236c8d9509b2b301ab349ffa  nginx-1.11.8.tar.gz
ac7e3153ab698b4cde077f0d5d7ac0a58897927eb36cf3b58cb01268ca0296f1d589c0a5b4f889b96b5b4a57bef05b17c59be59a9d7c4d7a3d3be58f101f7f41  nginx.conf
564958e7169d2cefac38733f8afafe250fc58626648ec3cb028be096c3f6145c0e51fd15291c62563fb733054639e0dbfb832019da12a9ab26113e9ffd12db74  default.conf"
