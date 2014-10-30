git-media
====================

Git media allows you to use Git with large media files
without storing the media in Git itself.

.. contents::


How it works
--------------------

Media file types are defined in .gitattributes, and smudge/clean
filters are then used to transform between a media file's hash and its
contents. Only the hash is stored in git.

Details
~~~~~~~~~~~~~~~~~~~~

TODO: edit/remove... Staging files with those extensions will automatically copy them
to the media buffer area (.git/media) until you run 'git media sync'
wherein they are uploaded.  Checkouts that reference media you don't
have yet will try to be automatically downloaded, otherwise they are
downloaded when you sync.

Note about fork
--------------------

This experimental fork of the "old official git media"
(schacon/git-media) exists because I don't yet understand the
rationale behind certain changes in the "new official git media"
(alebedev/git-media). Maybe I'll catch up eventually :) 

Claimed issues with alebedev/git-media:

* sync'd files are apparently never shown as being different (TODO:
add issue at alebedev/git-media).

* TODO: (add the others)

Claimed issues with schacon/git-media:

* filename/string handling (windows, unicode, etc) TODO: clarify

Installation
--------------------

TODO: unlikely to be right; needs updating, cleaning, and checking!

Requires:

* Git >= 2.0?

* Ruby >= ? plus: trollop

Optional (depending on transport required):

* s3
* ruby-atmos-pure
* right_aws

E.g. Ubuntu users:
 
        $ sudo apt-get install ruby 

   TODO: then ...? 

        $ sudo gem install bundler
        $ bundle install
        $ gem build git-media.gemspec
        $ sudo gem install git-media-0.1.2.cb.1.gem

E.g. Windows users:
   
   TODO: ruby installer then ...?

        $ gem install bundler
        $ bundle install
        $ gem build git-media.gemspec
        $ gem install git-media-0.1.2.cb.1.gem


Configuration
--------------------

Global
~~~~~~~~~~~~~~~~~~~~

Tell git to use git-media's filters for media files:

	$ git config filter.media.clean "git-media filter-clean"
	$ git config filter.media.smudge "git-media filter-smudge"

Per repository
~~~~~~~~~~~~~~~~~~~~

* Tell git which files are media:

	$ echo "*.mov filter=media -crlf" > .gitattributes

* Tell git where to store the media.

There are four options (NOTE: I am currently only using the local
option, combined with external peer-to-peer sync):

1. Storing remotely in Amazon's S3
2. Storing locally in a filesystem path
3. Storing remotely via SCP (should work with any SSH server)
4. Storing remotely in atmos

Add a relevant subset of the following to your repository's .git/config:

```ini
[git-media]
	transport = <scp|local|s3|atmos>

	# settings for scp transport
	scpuser = <user>
	scphost = <host>
	scppath = <path_on_remote_server>

	# settings for local transport
	path = <local_filesystem_path>

	# settings for s3 transport
	s3bucket = <name_of_bucket>
	s3key    = <s3 access key>
	s3secret = <s3 secret key>

	# settings for atmos transport
	endpoint = <atmos server>
	uid      = <atmos_uid>
	secret   = <atmos secret key>
	tag      = <atmos object tag>
```

TODO: document the purpose of media.auto-download

Usage
--------------------

Summary: do `git media sync` after clone, push, pull.

Sharing your work
~~~~~~~~~~~~~~~~~~~~

Use your usual git workflow, eg:

	$ git add newbigfile.mov
        $ git commit -m "Something something."
        $ git push

Then upload your new/changed media:

	$ git media sync

Getting other people's work
~~~~~~~~~~~~~~~~~~~~

Use your usual git workflow, eg:

        $ git pull

Then get new/changed media:

        $ git media sync        


TODO: Might be room for improvement (e.g. git status/diff could
indicate when diffs are from unexpanded media rather than changed
media, although git status tells you this).

More
~~~~~~~~~~~~~~~~~~~~

You can also check the status of your media files via

	$ git media status

Which will show you files that are waiting to be uploaded and how much data
that is. 

TODO: If you want to upload & delete the local cache of media files, run:

	$ git media clear

Troubleshooting
--------------------

TODO: need to update

(Windows) It is important to switch off git smart newline character support for media files.
Use `-crlf` switch in `.gitattributes` (for example `*.mov filter=media -crlf`) or config option `core.autocrlf = false`.

(Windows) You might run into a problem verifying certificates
for S3 or something. If that happens, modify

	C:\Ruby191\lib\ruby\gems\1.9.1\gems\right_http_connection-1.2.4\lib\right_http_connection.rb

And add at line 310, right before `@http.start`:

      @http.verify_mode     = OpenSSL::SSL::VERIFY_NONE

Release notes
--------------------

0.1.2.cb.1
~~~~~~~~~~~~~~~~~~~~

* Fixed handling of line endings in smudge and clean filters
* Fixed finding references on Windows

0.1.2.cb.0
~~~~~~~~~~~~~~~~~~~~

* Initial (unreleased) version; no code changes from
https://github.com/schacon/git-media/commit/42abe20ab95dd18e3f5e83fb190142abd36f2e3e

Copyright
--------------------

Original work: Copyright (c) 2009 Scott Chacon. See LICENSE for details.

Modified work: All changes from the original work are in the public
domain, provided as-is, with no warranty of any kind expressed or
implied.  Anyone is free to copy, modify, publish, use, compile, sell,
or distribute the changes under any license, for any purpose,
commercial or non-commercial, and by any means.



