_cfgdir=/etc/nginx
_tmpdir=/var/lib/nginx

pkgname=nginx-passenger-hg
pkgver=5204.a64c8a5da336
pkgrel=1
pkgdesc='Lightweight HTTP server and IMAP/POP3 proxy server'
arch=('i686' 'x86_64')
depends=('pcre' 'zlib' 'openssl' 'geoip')
makedepends=('passenger' 'mercurial')
url="http://nginx.org"
license=('custom')
backup=(${_cfgdir:1}/fastcgi.conf
		${_cfgdir:1}/fastcgi_params
		${_cfgdir:1}/koi-win
		${_cfgdir:1}/koi-utf
		${_cfgdir:1}/mime.types
		${_cfgdir:1}/nginx.conf
		${_cfgdir:1}/scgi_params
		${_cfgdir:1}/uwsgi_params
		${_cfgdir:1}/win-utf
		etc/logrotate.d/nginx)
source=(hg+http://hg.nginx.org/nginx/
        service
        logrotate)
sha256sums=('SKIP'
            '77da8ce4d8378048606a25e09270ee187d6b226ee750b6cb4313af5549f5156a'
            '9523a1fdd5eb61bf62f3049f6ee088b198e36d5edcce2d9b08bbeb2930aa5a16')
conflicts=('nginx')
provides=('nginx')

_hgrepo=nginx

pkgver() {
  cd "$srcdir/$_hgrepo"
  echo $(hg identify -n).$(hg identify -i)
}

build() {
    cd "$srcdir"
    rm -rf "$srcdir/$_hgrepo-build"
    cp -r "$srcdir/$_hgrepo" "$srcdir/$_hgrepo-build"
    cd "$srcdir/$_hgrepo-build"


    ./auto/configure \
        --prefix=$_cfgdir \
        --conf-path=$_cfgdir/nginx.conf \
        --sbin-path=/usr/sbin/nginx \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/lock/nginx.lock \
        --user=http --group=http \
        --http-log-path=/var/log/nginx/access.log \
        --error-log-path=/var/log/nginx/error.log \
        --http-client-body-temp-path=$_tmpdir/client-body \
        --http-proxy-temp-path=$_tmpdir/proxy \
        --http-fastcgi-temp-path=$_tmpdir/fastcgi \
        --http-scgi-temp-path=$_tmpdir/scgi \
        --http-uwsgi-temp-path=$_tmpdir/uwsgi \
        --with-imap --with-imap_ssl_module \
        --with-ipv6 --with-pcre-jit \
        --with-file-aio \
        --with-http_dav_module \
        --with-http_geoip_module \
        --with-http_gunzip_module \
        --with-http_gzip_static_module \
        --with-http_realip_module \
        --with-http_spdy_module \
        --with-http_ssl_module \
        --with-http_stub_status_module \
        --add-module=/usr/lib/passenger/ext/nginx \
        #--with-http_mp4_module \
        #--with-http_addition_module \
        #--with-http_xslt_module \
        #--with-http_image_filter_module \
        #--with-http_sub_module \
        #--with-http_flv_module \
        #--with-http_random_index_module \
        #--with-http_secure_link_module \
        #--with-http_degradation_module \
        #--with-http_perl_module \

    make
}

package() {
    cd "$srcdir/$_hgrepo-build"
    make DESTDIR="$pkgdir" install

    sed -e 's|\<user\s\+\w\+;|user html;|g' \
        -e '44s|html|/usr/share/nginx/html|' \
        -e '54s|html|/usr/share/nginx/html|' \
        -i "$pkgdir"/etc/nginx/nginx.conf
    rm "$pkgdir"/etc/nginx/*.default

    install -d "$pkgdir"/$_tmpdir
    install -dm700 "$pkgdir"/$_tmpdir/proxy

    chmod 750 "$pkgdir"/var/log/nginx
    chown http:log "$pkgdir"/var/log/nginx

    install -d "$pkgdir"/usr/share/nginx
    mv "$pkgdir"/etc/nginx/html/ "$pkgdir"/usr/share/nginx

    install -Dm644 "$srcdir"/logrotate "$pkgdir"/etc/logrotate.d/nginx
    install -Dm644 "$srcdir"/service "$pkgdir"/usr/lib/systemd/system/nginx.service
    install -Dm644 docs/text/LICENSE "$pkgdir"/usr/share/licenses/nginx/LICENSE
    rm -rf "$pkgdir"/var/run
}
