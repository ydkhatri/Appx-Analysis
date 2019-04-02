## Getting App details

```
SELECT DISTINCT * FROM 
(SELECT substr(packfam.PackageFamilyName, instr(packfam.PackageFamilyName, '.') + 1, 
         instr(packfam.PackageFamilyName, '_') -2 - instr(packfam.PackageFamilyName, '.') + 1 ) as AppName, 
  datetime((packuser.installTime/10000000) -11644473600, 'unixepoch') InstallTime, 
  CASE WHEN instr(pack.PublisherDisplayName, 'ms-resource')=0 THEN pack.PublisherDisplayName 
      ELSE '' END AS PublisherDisplayName,
  packfam.PublisherId, packuser.User, userkey.UserSid,
  CASE Architecture 
	WHEN 0 THEN 'X64' 
	WHEN 9 THEN 'x86' 
	WHEN 11 THEN 'Neutral' 
	ELSE Architecture END AS Architecture, 
  substr(pack.packageFullName, instr(pack.packageFullName, '_') + 1, 
         instr(substr(pack.packageFullName, instr(pack.packageFullName, '_') + 1), '_') - 1) AS version,  
  CASE SignatureOrigin 
	WHEN 3 THEN 'System' 
	WHEN 2 THEN 'Store' 
	ELSE 'Unknown' END AS SignatureKind,
  packloc.installedLocation 
  FROM PackageUser packuser, package pack, MrtPackage mrt, packageFamily packfam, packageLocation packloc, User userkey
  WHERE packuser.package = pack._PackageId 
	AND pack.packageFamily = packfam._PackagefamilyId 
	AND packuser.user = userkey._UserID
	AND packloc.package = pack._packageId 
	AND (pack.resourceId IS NULL OR pack.resourceId = 'neutral')
)
```

## Get App Installation History

```
SELECT User, HResult, 
(SELECT PackageFullName from PackageIdentity 
  WHERE PackateIdentity._PackageIdentityID=DeploymentHistory.PackageIdentity) as PkgName, 
datetime(WhenOccurred / 10000000) - 11644473600, 'unixepoch') when_occurred
FROM DeloymentHistory
```
