# Clover Update Steps

* Clone `boot` to backup
* Mount `backup` EFI partition
* Backup `boot` EFI config
  * Keep backups of historical EFI config with datestamps
* Boot from `backup` to confirm
* Reboot into `boot`
* Update `clover`
  * Check what it's trying to install
* If all good, copy new clover config to `backup` disk EFI partition so they match
* Test  `backup`  still boots correctly

Get the latest Clover build from [https://www.tonymacx86.com/resources/categories/clover-builds.12/](https://www.tonymacx86.com/resources/categories/clover-builds.12/)

