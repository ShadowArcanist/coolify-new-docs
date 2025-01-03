---
title: "Self-update"
---

Within the `Settings` page, you can configure the update settings for your Coolify instance.

## How it works

By default Coolify checks for updates periodically based on the cron syntax that is provided.

These settings are split in to two categories:

- **Update Check Frequency**: Cron expression for update check frequency (check for new Coolify versions and pull new Service Templates from CDN). Default is every hour.
- **Auto Update Frequency**: Cron expression for auto update frequency (automatically update coolify). Default is every day at 00:00

By default on self hosted instances the `Auto Update Enabled` should be preticked.

## Cron Syntax

The cron syntax supports the base cron syntax as well as the following strings:

```js
const VALID_CRON_STRINGS = [
    'every_minute' => '* * * * *',
    'hourly' => '0 * * * *',
    'daily' => '0 0 * * *',
    'weekly' => '0 0 * * 0',
    'monthly' => '0 0 1 * *',
    'yearly' => '0 0 1 1 *',
];
```

## Update Check Frequency

This setting is used to check for new Coolify versions and pull new Service Templates from the CDN. The default value is every hour.

Please note that disabling `Auto Update Enabled` will not disable the update check frequency, meaning you can control when to update Coolify.

## Auto Update Frequency

This setting is used to automatically update Coolify. The default value is every day at 00:00.
