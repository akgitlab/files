<?php

# To enable this configuration, you need to add to the file /var/www/mediawiki/LocalSettings.php this lines
# Settings for LDAP integration
# include "scripts/ldap.php";

# Enable required auth and access extensions
wfLoadExtension( 'Auth_remoteuser' );
wfLoadExtension( 'LDAPAuthentication2' );
wfLoadExtension( 'LDAPAuthorization' );
wfLoadExtension( 'LDAPGroups' );
wfLoadExtension( 'LDAPProvider' );
wfLoadExtension( 'LDAPUserInfo' );
wfLoadExtension( 'OATHAuth' );
wfLoadExtension( 'PluggableAuth' );

# Enable logging / debugging settings
#    https://www.mediawiki.org/wiki/Manual:How_to_debug#Logging
#    # Enable/Disable logging for debugging.
#    # Verbose but really helped me figure out what was going on as I could tail follow all the logs
#    # as I worked on the config. The online docs were not enough.
#
$wgShowExceptionDetails = true ;
#$wgDevelopmentWarnings = true;
## $wgShowDebug = false;  # enable some debugging on screen
## $wgDebugToolbar = true;  # http://www.mediawiki.org/wiki/Manual:How_to_debug
$wgDebugLogFile = "/var/log/mediawiki/debug-${wgDBname}.log" ;
## $wgDBerrorLog = "/var/log/mediawiki/dberror.log" ;
$wgRateLimitLog = "/var/log/mediawiki/ratelimit.log" ;
// this finally worked. ldap.log got a log from PluggableAuth
$wgDebugLogGroups = array(
     'BMWExtension' => "/var/log/mediawiki/bmwextension.log",
     'resourceloader' => '/var/log/mediawiki/resourceloader.log',
     'exception' => '/var/log/mediawiki/exception.log',
     'error' => '/var/log/mediawiki/error.log',
     'exception-json' => '/var/log/mediawiki/exception.json',
     'Auth_remoteuser' => '/var/log/mediawiki/Auth_remoteuser.log',
     'LDAPAuthentication2' => '/var/log/mediawiki/LDAPAuthentication2.log',
     'LDAPAuthorization' => '/var/log/mediawiki/LDAPAuthorization.log',
     'LDAPGroups' => '/var/log/mediawiki/LDAPGroups.log',
     'LDAPUserInfo' => '/var/log/mediawiki/LDAPUserInfo.log',
     'LDAPProvider' => '/var/log/mediawiki/LDAPProvider.log',
     'PluggableAuth' => '/var/log/mediawiki/PluggableAuth.log',
     'LDAP' => '/var/log/mediawiki/ldap.log',
     'MediaWiki\\Extension\\LDAPProvider\\Client' => '/var/log/mediawiki/LDAPClient.log'
);

#############################################
##
##       Group Permissions
##
# The following permissions were set based on your choice in the installer
# commented out after initial setup
#$wgGroupPermissions['*']['edit'] = false;
## If account creation by anonymous users is forbidden, then allow
## it to be created automatically (by the extension) with these two settings: .
$wgGroupPermissions['*']['createaccount'] = true;
$wgGroupPermissions['*']['autocreateaccount'] = true;

###############################################
##
##    BEGIN LDAP STACK SETUP
##
###############################################
##
##      LDAPGroups
##      https://www.mediawiki.org/wiki/Extension:LDAPGroups
##
# Allows registration of custom group sync mechanisms.
$wgLDAPGroupsSyncMechanismRegistry = "\\MediaWiki\\Extension\\LDAPGroups\\SyncMechanism\\MappedGroups::factory"; 


##########################################
##
##   AuthRemoteUser
##   This is Authorization which is after Authentication
##   https://www.mediawiki.org/wiki/Extension:Auth_remoteuser
##   See Group Permissions Section also
##
$wgAuthRemoteuserDomain = "5-55.ru";
$wgAuthRemoteuserMailDomain = "5-55.ru";
$wgAuthRemoteuserNotify = true; 


###############################################
##
##      DOMAIN Config load
##
### $ldapJsonFile = "/etc/mediawiki/ldapprovider.json"  ;# traditional location. Created new ones w/symlinks to easliy change/modify and add new vhosts auth.
###
### The way I load this allows for more verbose errors in the log as my logging details helped a lot get this configured. Its verbose but it gets things done and I can shut this off and/or enable regular custom logrotate for this.
### 
$ldapJsonFile = "/etc/mediawiki/ldapprovider.json" ;
$ldapErrMsg = "LDAPStack Error - LDAP JSON FNF or $IP/extensions/LDAPProvider not found [LDAP-ERR]. : $ldapJsonFile " ;
$ldapConfig = false;
$ldapWriteOkMsgs = false;
if (is_file($ldapJsonFile) && is_dir("$IP/extensions/LDAPProvider")) {
  #
  # this adds a good bit of log messages to files for fcgi and others which show under 'error' heading but it was the only way I could write the file
  # as there is no other easily usable facility I can find in mediawiki to write to a log file but error_log
  #
  if( $ldapWriteOkMsgs ) { error_log("LDAPStack [OK] - JSON in file and the LDAPProvider extensions folder. [LDAP-1000] : $ldapJsonFile "); }

  $testJson = @json_decode(file_get_contents($ldapJsonFile),true);
  if (is_array($testJson)) {
    $ldapConfig = true;

    if( $ldapWriteOkMsgs ) { error_log("LDAPStack OK - Found and validated JSON in file. [LDAP-1001] : $ldapJsonFile "); }
  }
  if (! is_array($testJson)) {
      error_log("LDAPStack Error 103 - $ldapJsonFile syntax error.");
  };
  if (! $ldapConfig ) {
      error_log("LDAPStack Error 102 - Found invalid JSON file or FNF. : $ldapJsonFile ");
  };

};

if (! is_file( $ldapJsonFile ) ) {
    error_log("LDAPStack Error 101 - Found invalid JSON file or FNF. : $ldapJsonFile ");
};

###############################################
##
##     LDAPProvider
##
$LDAPProviderDomainConfigs = "/etc/mediawiki/ldapprovider.json" ;
$LDAPProviderCacheType = "CACHE_ANYTHING" ;
$LDAPProviderCacheTime = 500 ;
$LDAPProviderDomainConfigProvider = "\\MediaWiki\\Extension\\LDAPProvider\\DomainConfigProvider\\LocalJSONFile::newInstance" ;
$LDAPProviderDefaultDomain = '5-55.ru' ;

##############################################
##
##     LDAPAuth
##     See https://www.mediawiki.org/wiki/Extension:LDAPAuthorization
##
$wgLdapAuthDomainNames = '5-55.ru' ;
$wgLdapAuthIsActiveDirectory = '5-55.ru';
$wgLdapAuthSearchTree = true ;

###############################################
##
##     LDAPAuthentication2
#
#      wfLoadExtension( 'LDAPAuthentication2' );
#      https://www.mediawiki.org/wiki/Extension:LDAPAuthentication2
#      https://www.mediawiki.org/wiki/Extension:LDAPAuthentication2#Configuration
#
$LDAPAuthentication2AllowLocalLogin = true ;
$LDAPAuthentication2UsernameNormalizer = 'strtolower';

################################################
##
##     PluggableAuth
##     extension $wgPluggableAuth which is required for LDAP
##     https://www.mediawiki.org/wiki/Extension:PluggableAuth
##
$wgPluggableAuth_EnableAutoLogin = true ; 
$wgPluggableAuth_EnableLocalProperties = false ; 
$wgPluggableAuth_ButtonLabel = "Войти";

# In version mediawiki 1.39 did not work without these lines
# https://www.mediawiki.org/wiki/Extension_talk:LDAPAuthentication2
$wgPluggableAuth_Class="MediaWiki\\Extension\\LDAPAuthentication2\\PluggableAuth";
$wgPluggableAuth_Config['Войти'] = [
    'plugin' => 'LDAPAuthentication2',
    'data' => [
        'domain' => '5-55.ru'
    ]
];

################################################
##
##     END LDAP Stack Settings
##
################################################

### END LocalSettings.php ###

?>
