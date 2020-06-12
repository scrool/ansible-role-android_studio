# Ansible Role: `android_studio`

Installs or uninstalls [Android
Studio](https://developer.android.com/studio/) - the official Integrated
Development Environment (IDE) for Android app development, based on IntelliJ
IDEA. Creates XDG desktop file to launch the application. Configures inotify
watches limit.

## Requirements

- A recent version of Ansible. Tested on 2.9. It might work on previous versions.
- Fedora 31. It might work on other versions.
- Access to an archive of Android Studio release. Either by URL to download or
  path to a file.
- Package `desktop-file-utils` installed or role installs this from configured
  repositories which might connect to internet.

## Role Variables

All variables which can be overridden are stored in
[defaults/main.yml](defaults/main.yml) file and listed in a table bellow as
well.

| Name                                      | Default Value                 | Description  |
| ----------------------------------------- | ----------------------------- | ------------ |
| `android_studio_archive_src`              | ""                            | Path to an release archive of Android Studio on a control host or URL to download the archive from. |
| `android_studio_state`                    | present                       | By default installs the program. Set to "absent" to uninstall. |
| `android_studio_top_dest_name`            | android-studio                | Base name of installation directory and name part of a desktop file. |
| `android_studio_desktop_name`             | Android Studio                | Name entry of desktop file. |
| `android_studio_install_parent_dir`       | /opt                          | Parent directory in which program will be installed. See also `android_studio_top_dest_name`.
| `android_studio_desktop_parent_dir`       | /usr/local/share/applications | Parent directory in which desktop file will be installed. See also `android_studio_top_dest_name`.
| `android_studio_force_remove`             | no                            | If set to "yes", contents of installation directory and desktop file are not compared against contents of `android_studio_archive_src` when uninstall, upgrade or downgrade.
| `android_studio_inotify_max_user_watches` | 524288                        | Sets `fs.inotify.max_user_watches`. See [Inotify Watches Limit on IntelliJ IDEA documentation](https://confluence.jetbrains.com/display/IDEADEV/Inotify+Watches+Limit) for more details.

`android_studio_archive_src` must be overriden from its default value.

## Example Playbook

### Install

To install the program, define path to a Android Studio release archive from the
internet:

```yaml
- hosts: all
  roles:
    - role: scrool.android_studio
      vars:
        android_studio_archive_src: https://dl.google.com/dl/android/studio/ide-zips/3.6.1.0/android-studio-ide-192.6241897-linux.tar.gz
```

Role fails early `buildNumber` from `product-info.json` differs between
provided archive and installed version.

To install two versions side by side (re)define
`android_studio_top_dest_name` and `android_studio_desktop_name`,
e.g.:

```yaml
- hosts: all
  roles:
    - role: scrool.android_studio
      vars:
        android_studio_archive_src: https://dl.google.com/dl/android/studio/ide-zips/4.0.0.14/android-studio-ide-193.6401094-linux.tar.gz
        android_studio_top_dest_name: android-studio-beta
        android_studio_desktop_name: Android Studio Beta
```

### Uninstall

To uninstall, set same values as for install. By default role removes content
only if installed directory, extracted icon and desktop file matches what it
would install. Finally set state variable to "absent".

Specify local path to a downloaded release archive file:

```yaml
- hosts: all
  roles:
    - role: scrool.android_studio
      vars:
        android_studio_archive_src: /tmp/android-studio-ide-192.6241897-linux.tar.gz
        android_studio_state: absent
```

Alternatively you can enable option `android_studio_force_remove` and again set
state variable to "absent". In this case role won't need nor even check
`android_studio_archive_src`. This effectively removes installation dir and
desktop entry file. Example:

```yaml
- hosts: all
  roles:
    - role: scrool.android_studio
      vars:
        android_studio_state: absent
        android_studio_force_remove: yes
```

## License

This project is licensed under MIT License. See [LICENSE](LICENSE) for more
details.

## Author Information

- [Pavol Babinčák](https://github.com/scrool)
