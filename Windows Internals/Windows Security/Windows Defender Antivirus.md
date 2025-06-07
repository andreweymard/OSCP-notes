* Features
	* `Real-time protection,` which protects the device from known threats in real-time and cloud-delivered protection, which works in conjunction with automatic sample submission to upload suspicious files for analysis.
	* `Tamper Protection,` which prevents security settings from being changed through the Registry, PowerShell cmdlets, or group policy.
	* `Controlled folder access` is Defender's built-in Ransomware protection.


``` powershell
PS C:\Users\andre> Get-MpComputerStatus

AMEngineVersion                  : 1.1.25030.1
AMProductVersion                 : 4.18.25030.2
AMRunningMode                    : Normal
AMServiceEnabled                 : True
AMServiceVersion                 : 4.18.25030.2
AntispywareEnabled               : True
AntispywareSignatureAge          : 0
AntispywareSignatureLastUpdated  : 06-May-25 11:49:56 PM
AntispywareSignatureVersion      : 1.427.671.0
AntivirusEnabled                 : True
AntivirusSignatureAge            : 0
AntivirusSignatureLastUpdated    : 06-May-25 11:49:52 PM
AntivirusSignatureVersion        : 1.427.671.0
BehaviorMonitorEnabled           : True
ComputerID                       : 9A466DDA-4B3A-40A8-BB0E-E7E02E257E7F
ComputerState                    : 0
DefenderSignaturesOutOfDate      : False
DeviceControlDefaultEnforcement  :
DeviceControlPoliciesLastUpdated : 27-Mar-23 7:14:58 PM
DeviceControlState               : Disabled
FullScanAge                      : 1257
FullScanEndTime                  : 27-Nov-21 9:10:09 AM
FullScanOverdue                  : False
FullScanRequired                 : False
FullScanSignatureVersion         : 1.353.1638.0
FullScanStartTime                : 27-Nov-21 6:59:26 AM
InitializationProgress           : ServiceStartedSuccessfully
IoavProtectionEnabled            : True
IsTamperProtected                : True
IsVirtualMachine                 : False
LastFullScanSource               : 1
LastQuickScanSource              : 2
NISEnabled                       : True
NISEngineVersion                 : 1.1.25030.1
NISSignatureAge                  : 0
NISSignatureLastUpdated          : 06-May-25 11:49:52 PM
NISSignatureVersion              : 1.427.671.0
OnAccessProtectionEnabled        : True
ProductStatus                    : 524288
QuickScanAge                     : 2
QuickScanEndTime                 : 04-May-25 12:09:52 PM
QuickScanOverdue                 : False
QuickScanSignatureVersion        : 1.427.614.0
QuickScanStartTime               : 04-May-25 12:04:54 PM
RealTimeProtectionEnabled        : True
RealTimeScanDirection            : 0
RebootRequired                   : False
SmartAppControlExpiration        :
SmartAppControlState             : Off
TamperProtectionSource           : N/A
TDTCapable                       : Supported
TDTMode                          : rsw
TDTSiloType                      : E
TDTStatus                        : Enabled
TDTTelemetry                     : Disabled
TroubleShootingDailyMaxQuota     :
TroubleShootingDailyQuotaLeft    :
TroubleShootingEndTime           :
TroubleShootingExpirationLeft    :
TroubleShootingMode              :
TroubleShootingModeSource        :
TroubleShootingQuotaResetTime    :
TroubleShootingStartTime         :
PSComputerName                   :
```
