GNOME Software Release Notes
===

Release schedule
---

GNOME Software releases are done on the timetable set by the [GNOME release schedule](https://wiki.gnome.org/Schedule).

Maintainers take it in turns to make releases so that the load is spread out evenly.

Making a release
---

Adapted from the [GNOME release process](https://wiki.gnome.org/MaintainersCorner/Releasing).

Make sure your repository is up to date and doesn’t contain local changes:
```
git pull
git status
```

Check the version in `meson.build` is correct for this release.

Write release entries:
```
gitlab-changelog GNOME/gnome-software 3.38.0..
```

Edit this down to just the user visible changes, and list them in
`data/appdata/org.gnome.Software.appdata.xml.in`. User visible changes are ones
which the average user might be interested to know about, such as a fix for an
impactful bug, a UI change, or a feature change.

You can get review of your appdata changes from other co-maintainers if you wish.

Generate `NEWS` file:
```
appstream-util appdata-to-news ../data/appdata/org.gnome.Software.appdata.xml.in > NEWS
```

Commit the release:
```
git add -p
git commit -m "Release version 3.38.1"
```

Build the release tarball:
```
ninja dist
```

Tag, sign and push the release (see below for information about `git evtag`):
```
git evtag sign 3.38.1
git push --atomic origin master 3.38.1
```

Upload the release tarball:
```
scp meson-dist/*.tar.xz master.gnome.org:
ssh master.gnome.org ftpadmin install gnome-software-*.tar.xz
```

Post release version bump in `meson.build`
```
git commit -a -m "trivial: Post release version bump"
git push
```

`git-evtag`
---

Releases should be done with `git evtag` rather than `git tag`, as it provides
stronger security guarantees. See
[its documentation](https://github.com/cgwalters/git-evtag) for more details.
In particular, it calculates its checksum over all blobs reachable from the tag,
including submodules; and uses a stronger checksum than SHA-1.

You will need a GPG key for this, ideally which has been signed by others so
that it can be verified as being yours. However, even if your GPG key is
unsigned, using `git evtag` is still beneficial over using `git tag`.