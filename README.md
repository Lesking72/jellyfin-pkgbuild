# Jellyfin 10.9.x PKGBUILD

Note: This PKGBUILD is modified from `jellyfin-git` on the AUR, not the official repo package. Changes in Jellyfin's packaging process made this seem like a good idea at the time... then I spent the next several hours changing it to match the behavior of the repo package so installing it won't require manual intervention. It builds both `jellyfin-server` and `jellyfin-web`.


## Build dependencies
- `dotnet-sdk` >=8
- `npm`

## Runtime dependencies
- `dotnet-runtime` >=8
- `aspnet-runtime` >=8
- `jellyfin-ffmpeg`
- `sqlite`
