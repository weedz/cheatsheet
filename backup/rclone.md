# Backup (with restic, nextcloud, and rclone)

Reference:
- <https://docs.nextcloud.com/server/latest/user_manual/en/files/access_webdav.html>
- <https://rclone.org/commands/rclone_mount/>

## rclone

Use `rclone` to mount nextcloud using webdav:
1. Add a "App password" in Nextcloud under Settings > Security
2. Run `rclone config` and add a new remote.
  - Set url to `https://[url]/remote.php/dav/files/[username]/`
  - Enter the "App password" which was generated in step 1.
3. Mount a nextcloud directory with `rclone mount nextcloud:[path/to/remote] [local/path]`
