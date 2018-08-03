# magento2-PreserveModuleOrderingInProductionMode

- [Description](#description)
- [Install](#install)

## Description
**Issue:** When running `php bin/magento setup:upgrade --keep-generated` in production mode (which is recommended by by magento in their [deployment pipeline](https://devdocs.magento.com/guides/v2.2/config-guide/deployment/pipeline/technical-details.html#production-system)) the order of the modules in `app/etc/config.php` may change.
This is because, as part of the upgrade command the code at: vendor/magento/magento2-base/setup/src/Magento/Setup/Model/Installer.php:374 gets run, which may change the ordering of the modules. (As well as potentially enabling modules that are unknown. e.g If you have module A in your code base but it isn't specified in `app/etc/config.php` it will just deem that module enabled.

**Why this is a problem**
1. We use [Mdoq](https://www.mdoq.io/) which does our deployments for us, however after running the upgrade command there were changes to `app/etc/config.php` which meant further git checkouts failed.
2. There may be a time where the ordering of modules causes a bug, in this case you may have a bug in production that you can't replication in development because you haven't been lukcy/unlucky enough to get the same order of modules
3. Production setup differs from what is know

**Our solution** 
We decided to only preserve the order of modules when the site is in production mode, this means that production config will be in version control and should be identical. This does however mean that when the site isn't in production mode the configuration order will be changed. Although this does generate a bigger diff than expected, at least it is known. An can be tested safely on a [Mdoq](https://www.mdoq.io/) development instance.

## Install
`git apply --check preserve_ordering_in_production_mode.patch && git apply preserve_ordering_in_production_mode.patch`

`
