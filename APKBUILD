# Maintainer: Rocky Feng <folowing@gmail.com>

pkgname=nginx
pkgver=1.10.2
pkgrel=0
pkgdesc="HTTP and reverse proxy server"
url="http://www.nginx.org/en"
arch="all"
license="custom"

# Modules
depends=""
makedepends="linux-headers gd-dev geoip-dev libxml2-dev libxslt-dev
	libressl-dev pcre-dev perl-dev pkgconf zlib-dev"
pkgusers="nginx"
_grp_ngx="nginx"
_grp_www="www-data"
pkggroups="$_grp_ngx $_grp_www"
install="$pkgname.pre-install $pkgname.post-upgrade"
subpackages=""
replaces="$pkgname-common $pkgname-initscripts"
source="http://nginx.org/download/$pkgname-$pkgver.tar.gz
	nginx.conf
	default.conf
	$pkgname.logrotate
	$pkgname.initd
	ipv6.patch
	"
builddir="$srcdir/$pkgname-$pkgver"

_modules_dir="usr/lib/$pkgname/modules"
_modules="
	http-geoip
	http-image-filter
	http-perl
	http-xslt-filter
	mail
	stream"
for _m in $_modules; do
	subpackages="$subpackages $pkgname-mod-$_m:_module"
done


build() {
	cd "$builddir"

	./configure \
		--prefix=/var/lib/$pkgname \
		--sbin-path=/usr/sbin/$pkgname \
		--modules-path=/$_modules_dir \
		--conf-path=/etc/$pkgname/$pkgname.conf \
		--pid-path=/run/$pkgname/$pkgname.pid \
		--lock-path=/run/$pkgname/$pkgname.lock \
		--http-client-body-temp-path=/var/lib/$pkgname/tmp/client_body \
		--http-proxy-temp-path=/var/lib/$pkgname/tmp/proxy \
		--http-fastcgi-temp-path=/var/lib/$pkgname/tmp/fastcgi \
		--http-uwsgi-temp-path=/var/lib/$pkgname/tmp/uwsgi \
		--http-scgi-temp-path=/var/lib/$pkgname/tmp/scgi \
		--with-perl_modules_path=/usr/lib/perl5/vendor_perl \
		\
		--user=$pkgusers \
		--group=$_grp_ngx \
		--with-threads \
		--with-file-aio \
		--with-ipv6 \
		\
		--with-http_ssl_module \
		--with-http_v2_module \
		--with-http_realip_module \
		--with-http_addition_module \
		--with-http_xslt_module=dynamic \
		--with-http_image_filter_module=dynamic \
		--with-http_geoip_module=dynamic \
		--with-http_sub_module \
		--with-http_dav_module \
		--with-http_flv_module \
		--with-http_mp4_module \
		--with-http_gunzip_module \
		--with-http_gzip_static_module \
		--with-http_auth_request_module \
		--with-http_random_index_module \
		--with-http_secure_link_module \
		--with-http_slice_module \
		--with-http_stub_status_module \
		--with-http_perl_module=dynamic \
		--with-mail=dynamic \
		--with-mail_ssl_module \
		--with-stream=dynamic \
		--with-stream_ssl_module \
		|| return 1

	make || return 1
}

package() {
	cd "$builddir"

	make DESTDIR="$pkgdir" install || return 1

	cd "$pkgdir"

	install -Dm644 "$srcdir"/nginx.conf ./etc/$pkgname/nginx.conf
	install -Dm644 "$srcdir"/default.conf ./etc/$pkgname/conf.d/default.conf
	install -Dm755 "$srcdir"/$pkgname.initd ./etc/init.d/$pkgname
	install -Dm644 "$srcdir"/$pkgname.logrotate ./etc/logrotate.d/$pkgname

	install -dm755 ./etc/$pkgname/modules
	install -dm750 -o $pkgusers -g $_grp_ngx ./var/lib/$pkgname
	install -dm700 -o $pkgusers -g $_grp_ngx ./var/lib/$pkgname/tmp
	install -dm755 -g $_grp_www ./var/www/localhost/htdocs

	install -dm755 ./var/log
	mv ./var/lib/$pkgname/logs ./var/log/$pkgname || return 1

	ln -sf /$_modules_dir ./var/lib/$pkgname/modules
	ln -sf /var/log/$pkgname ./var/lib/$pkgname/logs
	ln -sf /run/$pkgname ./var/lib/$pkgname/run

	rm -rf ./run ./etc/$pkgname/*.default
}

_module() {
	local name="${subpkgname#$pkgname-mod-}"
	name="${name//-/_}"
	local soname="$(eval "echo \$_${name}_so")";
	soname="${soname:-"ngx_${name}_module.so"}"

	pkgdesc="$pkgdesc (module $name)"
	depends="$pkgname $(eval "echo \$_${name}_depends")"
	provides="$(eval "echo \$_${name}_provides")"

	mkdir -p "$subpkgdir"/$_modules_dir
	cd "$subpkgdir"

	mv "$pkgdir"/$_modules_dir/$soname ./$_modules_dir/$soname || return 1

	mkdir -p "$subpkgdir"/etc/nginx/modules
	echo "load_module \"modules/$soname\";" > ./etc/nginx/modules/$name.conf
}

md5sums="e8f5f4beed041e63eb97f9f4f55f3085  nginx-1.10.2.tar.gz
256145c0f70d1d1d3b99f854553d48f0  nginx.conf
3d26969d4e77e0bc222ad9b32964f6f7  default.conf
db194cf3c6c4be12c70c757e0c9ad995  nginx.logrotate
16dcac0d7a2b406807d3377841d9b480  nginx.initd
801a87f7f9d27f8ad85b41a78b4c4461  ipv6.patch"
sha256sums="1045ac4987a396e2fa5d0011daf8987b612dd2f05181b67507da68cbe7d765c2  nginx-1.10.2.tar.gz
df873f301f947192c854994bb0e1bac46f73a5d3cf91df997f1b6a8ed26b5724  nginx.conf
c6b27adac2153ac5ca92aa0bcd933657bf8e17d88d7b1ab962768dd9fc6f310b  default.conf
b063611c6cb2d33bd43c4b17bf4135dda25f209bb77e4e66d1b156cffc37fbe6  nginx.logrotate
3d8a90d2f75b7f24c4d74722b5b3ac11d85f416c2d7641b4280d7c126bfe8395  nginx.initd
a24ef5843ae0afa538b00c37eb7da7870f9d7f146f52a9668678f7296cf71d9b  ipv6.patch"
sha512sums="f2d5a4fbabaf9333bae46461bcbe3dbcc5ff7e8f8c7a5dead3063e3d59c9ec15dc85262a23ca7d693db45a50ec98a70fb216b3da9872ee23d57b6bfaf064876e  nginx-1.10.2.tar.gz
ac7e3153ab698b4cde077f0d5d7ac0a58897927eb36cf3b58cb01268ca0296f1d589c0a5b4f889b96b5b4a57bef05b17c59be59a9d7c4d7a3d3be58f101f7f41  nginx.conf
564958e7169d2cefac38733f8afafe250fc58626648ec3cb028be096c3f6145c0e51fd15291c62563fb733054639e0dbfb832019da12a9ab26113e9ffd12db74  default.conf
09b110693e3f4377349ccea3c43cb8199c8579ee351eae34283299be99fdf764b0c1bddd552e13e4d671b194501618b29c822e1ad53b34101a73a63954363dbb  nginx.logrotate
1ea032cf88021ec8aa1401d284ea738364511cdb9f8c01670deb8e59aae570f5bbe17f0cbab73c0e08d6b342a621b6a9c014832168ed41f6028ecfa4211b60cf  nginx.initd
68d64a84568ec2df0366925ab282a05ebe21a85044b6c7844a47573cfd8cc8ed119cc772358bc3fff36e2d4fdf583a730592825f5f98632993ca86d1f8438d5f  ipv6.patch"
