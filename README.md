# ocrfeeder-flatpak

**OCRFeeder** is a complete Optical Character Recognition and Document Analysis and Recognition program.

[Homepage](https://wiki.gnome.org/Apps/OCRFeeder)

## FAQ

### Why not a RPM package?

I already provided [COPR repo](https://copr.fedorainfracloud.org/coprs/scx/ocrfeeder) with (S)RPM packages for EL and Fedora.

### Why sandboxed OCRFeeder doesn't recognize my scanner, while it works flawless on the host?

Because flatpak doesn't have access to `sane` from the host. Currently, we basically need to include each `sane` backend in the flatpak package, which is insane to maintenance. I tried my best, but I am unable to support all [external backends](http://www.sane-project.org/lists/sane-backends-external.html). What's worse, a lot of them require an insane amount of patches. Even if somehow I managed to include them here, I have no way to test them all.

There are some plans to extend [xdg desktop portals](https://github.com/flatpak/xdg-desktop-portal/issues/13), or - to be more specific - add the [scanner portal](https://github.com/flatpak/xdg-desktop-portal/issues/218), so it would provide a sane way to access scanners.
Unfortunately, these are just plans. No one is currently working on it.
By the way, that's one of the main reasons why we don't have the flatpak package for [simple-scan](https://gitlab.gnome.org/GNOME/simple-scan/issues/21) yet.

However, there is a relatively easy way to access scanner via the `net` backend.
First of all, you have to install `saned` (`sane` daemon) on the host.

Instruction for EL7:

```
# yum install sane-backends
```

Instruction for EL8:

```
# yum install sane-backends-daemon
```

Instruction for Fedora:

```
# dnf install sane-backends-daemon
```

Instruction for Debian/Ubuntu:

```
# apt-cache update && apt-get install sane-utils
```

Then, make sure to add `localhost` to the `/etc/sane.d/saned.conf` file:

```
# grep '^[[:blank:]]*localhost[[:blank:]]*$' "/etc/sane.d/saned.conf" || echo 'localhost' >> "/etc/sane.d/saned.conf"
```

Finally, enable and start the `saned` service:

```
# systemctl enable saned.socket
```

```
# systemctl start saned.socket
```

Now you should be able to use your scanner in this flatpak package.

See also:

* [Similar tutorial for Paperwork](https://gitlab.gnome.org/World/OpenPaperwork/paperwork/blob/master/flatpak/README.markdown#quick-start)
