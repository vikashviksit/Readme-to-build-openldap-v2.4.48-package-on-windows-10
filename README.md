# ReadMe-to-Build-Oplad-v2.4.48-package-on-Windows
Intorduction: 

This Readme list (list bullet is c++ pointer symbol '*') the additional set of changes over the ones stated at  https://github.com/mbooth101/openldap by Matt Booth that needs to be done to build OpenLdap package 2.4.48 on 64 bit Windows platform using VC build tool set v140 (VS2017 x64 command window) The source code of OpenLdap 2.4.48.tgz was downloaded from their Website(https://www.openldap.org/software/download/OpenLDAP/openldap-release/). 

Note:For best view switch to Raw format.

*openldap\include\portable.h - Conditional #def for windows platform - vsnprintf ,snprintf
#ifndef _WIN32
#define vsnprintf _vsnprintf
#endif
#ifndef _WIN32
#define snprintf _snprintf
#endif

*openldap\libraries\liblber\lber.def - Commented export function in the module definition file as linker unable to resolve this functions. 
ber_peek_element,ber_skip_element,ber_strnlen

*openldap\include\ac\socket.h - Conditional #def for windows platform
#ifndef HAVE_WINSOCK
#define EWOULDBLOCK WSAEWOULDBLOCK
#define EINPROGRESS WSAEINPROGRESS
#define ETIMEDOUT	WSAETIMEDOUT
#endif

*openldap\include\ac\time.h - include Winsock.h for timeval struct
#elif defined(HAVE_WINSOCK)
# include <Winsock.h>
#else


*C:\openldap\libraries\libldap\util-int.c  - Just before int ldap_pvt_get_hname(
#ifdef _WIN32
#undef HAVE_GETNAMEINFO
#endif


*C:\openldap\include\portable.h - Change deprecated functions to ISO compliant function
change
#define strcasecmp	stricmp 
to
#define strcasecmp	_stricmp

change 
#define strncasecmp	strnicmp
to
#define strncasecmp	_strnicmp

*C:\openldap\include\portable.h - Build without Openssl first
//#define HAVE_OPENSSL 1
//#define HAVE_TLS 1
//#define HAVE_OPENSSL_SSL_H 1

/* define if you have OpenSSL */
//#define HAVE_OPENSSL 1

/* Define to 1 if you have the <openssl/bn.h> header file. */
//#define HAVE_OPENSSL_BN_H 1

/* define if you have OpenSSL with CRL checking capability */
//#define HAVE_OPENSSL_CRL 1

/* Define to 1 if you have the <openssl/crypto.h> header file. */
//#define HAVE_OPENSSL_CRYPTO_H 1

/* Define to 1 if you have the <openssl/ssl.h> header file. */
//#define HAVE_OPENSSL_SSL_H 1



*Comment following using ";" in openldap\libraries\libldap_r\ldap_r.def and openldap\libraries\libldap\ldap.def if SSL is not included.

ldap_int_hostname
ldap_int_tls_config
ldap_int_tls_destroy
ldap_int_tls_impl
ldap_int_tls_start
ldap_pvt_tls_accept
ldap_pvt_tls_check_hostname
ldap_pvt_tls_ctx_free
ldap_pvt_tls_destroy
ldap_pvt_tls_get_my_dn
ldap_pvt_tls_get_option
ldap_pvt_tls_get_peer_dn
ldap_pvt_tls_get_strength
ldap_pvt_tls_init
ldap_pvt_tls_init_def_ctx
ldap_pvt_tls_inplace
ldap_pvt_tls_sb_ctx
ldap_pvt_tls_set_option
ldap_tls_inplace

*openldap\include\portable.h - Use either Winsock2 or Winsock - Comment HAVE_WINSOCK2
//#define HAVE_WINSOCK2 1


*openldap\Makefile.win - Comment statements for conditional selection of Platform Arch when using VS2017 x64 or VS2015 x64 command prompt and set "ARCH" to "x64 Release"

#!IF [$(COMSPEC) /c cl /nologo /? \
#	| $(SystemRoot)\System32\find.exe "x64" >NUL ] == 0
#ARCH=x64 Release
#!ELSE
#ARCH=Win32 Release
#!ENDIF

ARCH=x64 Release

*Copy libeay32.lib and ssleay32.lib into openldap\libraries\liblber\x64\Release or update the linker command to include a search path where it can find these libs (Say ../openssl/lib)
