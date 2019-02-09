# APK release and deploy
Deploy APK file with changelog for Android projects with CI/CD.
This script uploads the release APK file to Dropbox and sends an email, with download link and changelog.

This script is used in my GitLab CI/CD for Android projects. The process is described in this article [https://medium.com/@snd.oleg/android-gitlab-ci-cd-sign-deploy-3ad66a8f24bf](https://medium.com/@snd.oleg/android-gitlab-ci-cd-sign-deploy-3ad66a8f24bf)

Sample project that is using this script for CI/CD [https://gitlab.com/arbuz/android-ci-cd](https://gitlab.com/arbuz/android-ci-cd)

## Motivation
I created this for Gitlab CI, when I had a problem making build/release artifacts public.
This script is the last stage for Android CI/CD process and may be helpful in following cases:
- Early stage projects which are not present in any app market, but need a way of distribution.
- Enthusiastic CEO who wants to have the latest builds with the latest changes.
- Freelancer who needs a painless way to distribute his work to clients.
- Teams that want an easy way to send the app for regression tests.

## How does it work
### API
This project is using Dropbox for APK storage and Zapier for sending emails.
You will need Dropbox API key and Zapier hook URL.
### Changes
Changes are extracted from provides CHANGEOG file. Each version change should end with '##' and begin with a title, that starts with '#' char. Example:
```
# Version 1.05

Removed Google Maps Fragment
Added:
 - Settings crash
 - ANR when loading
##
# Version 1.04

Added Google Maps Fragment
Fixed:
 - Settings crash
 - ANR when loading
##
```
The script will extract the latest (1.05 in example) changes.
### Email template
The script is using email template file for email composing. The template can have placeholders for the following: app name, app version, download link, changes.
Example for app name CoolApp, version 1.05:
```
#subject
New {app_name} release, version {app_version}
#body
New version is available for download:
{app_download_url}

Changes:
{change_log}

This email was sent automatically, please do not reply.
```
This will compose the following email subject:
```
New CoolApp release, version 1.05
```
And email body:
```
New version is available for download:
https://www.dropbox.com/s/lk2lk321jl1/coolapp_1_05.apk?dl=1

Changes:
Removed Google Maps Fragment
Added:
 - Settings crash
 - ANR when loading

This email was sent automatically, please do not reply.
```
### Usage
Help:
```bash
python3 deploy.py -h
```
Run (all arguments required):
```bash
python3 deploy.py \
    --release.dir=app/build/outputs/apk/release \
    --app.name=CoolAppp \
    --dropbox.key=$DROPBOX_KEY \
    --dropbox.folder=build \
    --changelog.file=CHANGELOG \
    --template.file=template_file \
    --zapier.hook=$ZAPIER_HOOK \
    --email.to=me@myorg.com,them@myorg.com
```
