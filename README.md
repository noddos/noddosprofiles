# noddosprofiles
Device Profiles for the NoDDos client

## Introduction
The [NoDDos client](https://github.com/noddos/noddos) monitors network traffic in the home and dynamically applies device-specific ACLs to that traffic to stop a device from being used in a DDOS attack. Identifying devices and applying ACLs to their traffic flows is based on device profiles. This repository stores these files so people can contribute using pull requests to add new profiles or update existing ones. The device profiles in this repo get periodically compiled in to a complete file that gets digitally signed. The client downloads the [compiled file](https://www.noddos.io/config/DeviceProfiles.json) and validates its [signature](https://www.noddos.io/config/DeviceProfiles.json.sha256). For more information see [NoDDos website](https://www.noddos.io/).

## The file format
A Device Profile is a json object with the following fields

- DeviceProfileUuid
This is [UUID version 4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_.28random.29) string that uniquely identifies the profile. There is small script in tools/getuuid.py that can create one for you.

- LastUpdated
String with last date/time (in ISO8601 format) that an edit was made to this profile. The NoDDos client tools will invalidate any mappings between a device and this profile if that mapping was created before this date/time. The value must be manually set.

- UploadStats
Boolean specifying whether traffic stats for devices matching this profile should be uploaded to the cloud. Because of privacy concerns, the device profile for Windows PCs has this set to false.

- Manufacturer
User-friendly name of the manufacturer of the device.

- Manufacturer
User-friendly name of the model of the device.

- ThingClass
String specifying what type of device this is. Current values in use are: "Smart Home Hub", "OTT STB", "Phone Tablet", "Home Gateway", "Light Switch", "Security Camera", "Solar Gateway", "PC", "Game Console", "SmartTV".

- Identifiers
List of json objects specifying matching criteria to identify a device. If any of these match then the device is mapped to the profile.

Each identifier consists of the following fields:
- IdentifyConfidenceLevel
Possible values are Low, Medium or High. Going forward, the profile match with the highest confidence level will be selected.

- EnforceConfidenceLevel
Possible values are 'None', 'Low', 'Medium', 'High'. Going forward, ACLs will only be applied if the EnforceConfidenceLevel for the match exceed a configurable threshold.

- MustMatch
JSON object that contains match criteria that must be met

- MustContain
JSON object that contains match criteria where the listed items must be in the total set of items

There are several criteria that can be used in MustMatch. Strings can be wildcarded either at the beginning of the string or at the end of the string.
- SsdpManufacturer
- SsdpModelName
- DhcpVendor
- DhcpHostname
- Mac
- SsdpFriendlyName
- SsdpUdn
- SsdpSerialNumber
- SsdpManufacturerUrl
- SsdpModelUrl

The MustContain object only supports the DnsQueries criteria, which has a JSON list of FQDN strings as values.

There is no support yet for specifying ACLs as that has not yet been implemented.
