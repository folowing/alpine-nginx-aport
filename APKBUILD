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

	install -Dm644 LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
	install -Dm644 README "$pkgdir"/usr/share/doc/$pkgname/README

	install -Dm644 objs/$pkgname.8 \
		"$pkgdir"/usr/share/man/man8/$pkgname.8 || return 1

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

vim() {
	pkgdesc="$pkgdesc (vim syntax)"
	depends=

	mkdir -p "$subpkgdir"/usr/share/vim
	cp -r "$builddir"/contrib/vim "$subpkgdir"/usr/share/vim/vimfiles
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
c4759cd2812220ab542317f54fbbe755  default.conf
db194cf3c6c4be12c70c757e0c9ad995  nginx.logrotate
16dcac0d7a2b406807d3377841d9b480  nginx.initd
801a87f7f9d27f8ad85b41a78b4c4461  ipv6.patch"
sha256sums="1045ac4987a396e2fa5d0011daf8987b612dd2f05181b67507da68cbe7d765c2  nginx-1.10.2.tar.gz
88e05a99a8a7419066f5ae75966fb1efc409bad4522d14986da074554ae61619  ngx_devel_kit-0.3.0.tar.gz
1077da2229ac7d0a0215e9e6817e297c10697e095010d88f1adbd1add1ce9f4e  echo-nginx-module-0.60.tar.gz
2b00d8e0ad2a67152a9cee7b7ee67990c742d501412df912baaf1eee9bb6dc71  ngx-fancyindex-0.4.1.tar.gz
b2e8162cce2d24861b1ed5bbb30fc51d5215e3f4bb9d01f53fc344904d5911e7  headers-more-nginx-module-0.31.tar.gz
b98c4f648589bbf3e2c3d5fd18664e7a7ef89ac083e96c8e984fa919e7a7c073  lua-nginx-module-0.10.6.tar.gz
212e973a6fda6ce82344815baf6d2324200d1b5c49ac9c34cfc6f0fb5ebcafbb  nchan-1.0.4.tar.gz
b286689355442657650421d8e8398bd4abf9dbbaade65947bb0cb74a349cc497  nginx-upload-progress-module-0.9.2.tar.gz
f9491dd24390b0d5d70dfe3553edf3d14efeb7c7a81b4d4a20c5cfeaefc1141c  nginx-rtmp-module-1.1.10.tar.gz
df873f301f947192c854994bb0e1bac46f73a5d3cf91df997f1b6a8ed26b5724  nginx.conf
f53fd49af9b4bc308653abb85d9989879ce1fb48e43c508f5f45c84f74513865  default.conf
b063611c6cb2d33bd43c4b17bf4135dda25f209bb77e4e66d1b156cffc37fbe6  nginx.logrotate
3d8a90d2f75b7f24c4d74722b5b3ac11d85f416c2d7641b4280d7c126bfe8395  nginx.initd
a24ef5843ae0afa538b00c37eb7da7870f9d7f146f52a9668678f7296cf71d9b  ipv6.patch"
sha512sums="f2d5a4fbabaf9333bae46461bcbe3dbcc5ff7e8f8c7a5dead3063e3d59c9ec15dc85262a23ca7d693db45a50ec98a70fb216b3da9872ee23d57b6bfaf064876e  nginx-1.10.2.tar.gz
558764c9be913a4f61d0e277d07bf3c272e1ce086b3fadb85b693a7e92805cd9fca4da7a8d29c96e53fc0d23b331327d3b2561ff61f19d2330e7d5d35ac7d614  ngx_devel_kit-0.3.0.tar.gz
c455bee73cebd0752449472452d15614b9587ddd199263d366484ede890c4d108eacbbeaef31adc9dc7732b56ef2bfc73c0fef3366366db03a8ec3fdc27a985c  echo-nginx-module-0.60.tar.gz
ce0043ad4a2b638c5d99244d6caaa65ad142cea78884084a9aeca5a9593c68dbe508c9e4dd85dc5722eb63ef386612bffc48d4b6fc1487df244fbcb7a73bffe1  ngx-fancyindex-0.4.1.tar.gz
bc6b936dff9989af9eb97864e5e9499748ea8c73b2f49a24ad00d2a90bf77d1d743e6789f7bf3948a7baaaa44b0cc9f48a1c8a52bce9902a13c88e1f6673c6e1  headers-more-nginx-module-0.31.tar.gz
202aaf39381d151d5f456e5cd61c87b65fa20ece997f7e517a3eb3912af3a153747a674ac004dbcc474728567ff58436cfb408accd5e922d805b1d697c34774c  lua-nginx-module-0.10.6.tar.gz
70877b03c21fc3a9a91e9077387d639d9361ad016e520534bb758c34b923b4b2f5fc46559d454c75f471919aa1179ce0fe5f1cc0b86d68b19c54d31ac907ca67  nchan-1.0.4.tar.gz
c31c46344d49704389722325a041b9cd170fa290acefe92cfc572c07f711cd3039de78f28df48ca7dcb79b2e4bbe442580aaaf4d92883fd3a14bf41d66dd9d8c  nginx-upload-progress-module-0.9.2.tar.gz
bcc0aee3308af7c61bf01a5530fcf1dae938e6778306f6e3eb5995e6d0529f43d33b7ee2acb813d5a39acc92e4853d207a01e8e41b766a6e0dd07aade60cd98f  nginx-rtmp-module-1.1.10.tar.gz
ac7e3153ab698b4cde077f0d5d7ac0a58897927eb36cf3b58cb01268ca0296f1d589c0a5b4f889b96b5b4a57bef05b17c59be59a9d7c4d7a3d3be58f101f7f41  nginx.conf
0907f69dc2d3dc1bad3a04fb6673f741f1a8be964e22b306ef9ae2f8e736e1f5733a8884bfe54f3553fff5132a0e5336716250f54272c3fec2177d6ba16986f3  default.conf
09b110693e3f4377349ccea3c43cb8199c8579ee351eae34283299be99fdf764b0c1bddd552e13e4d671b194501618b29c822e1ad53b34101a73a63954363dbb  nginx.logrotate
1ea032cf88021ec8aa1401d284ea738364511cdb9f8c01670deb8e59aae570f5bbe17f0cbab73c0e08d6b342a621b6a9c014832168ed41f6028ecfa4211b60cf  nginx.initd
68d64a84568ec2df0366925ab282a05ebe21a85044b6c7844a47573cfd8cc8ed119cc772358bc3fff36e2d4fdf583a730592825f5f98632993ca86d1f8438d5f  ipv6.patch"
