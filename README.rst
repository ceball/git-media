git-media
====================

Git media allows you to use Git with large ("media") files without
storing the files in Git itself.

.. contents::


How it works
--------------------

Media file types (e.g. .mov) are defined in .gitattributes, and
smudge/clean filters are then used to transform between a media file's
hash and its contents (presenting the hash to git, but the contents to
the file system).


Note about fork
--------------------

This fork of the "old official git media" (schacon/git-media) exists
because I don't yet understand the rationale behind certain changes in
the "new official git media" (alebedev/git-media). Maybe I'll catch up
eventually :)


Installation
--------------------

Requires:

- Git >= ? (Windows: git bash, not cmd.exe)
- Ruby >= ? 
- Ruby gem: trollop

Optional, depending on transport required (see configuration, below):

- s3
- ruby-atmos-pure
- right_aws
- net_dav

Example installation on Ubuntu (note: I am a novice Ruby
programmer, so this may not be the best advice!)::

        $ sudo apt-get install ruby
        $ sudo gem install bundler

        $ bundle install
        $ gem build git-media.gemspec
        $ sudo gem install git-media-0.1.2.cb.1.gem

Example installation on Windows (note: I am a novice Ruby programmer,
so this may not be the best advice!)::
   
        # (ruby installer)
        $ gem install bundler

        $ bundle install
        $ gem build git-media.gemspec
        $ gem install git-media-0.1.2.cb.1.gem


Configuration
--------------------

Configuration involves (globally) telling git to invoke git-media's
filters for media files, and (per-repository) telling git which files
are media.

Global
~~~~~~~~~~~~~~~~~~~~

Tell git to use git-media's filters for media files::

        $ git config filter.media.clean "git-media filter-clean"
        $ git config filter.media.smudge "git-media filter-smudge"

Per repository
~~~~~~~~~~~~~~~~~~~~

First, tell git which files are media::

        $ echo "*.mov filter=media -crlf" >> .gitattributes

Note that you should disable git's smart newline support for media
files (i.e. use ``-crlf``).

Second, tell git where to store the media. There are four options
(NOTE: I am currently only using the local option, combined with
external peer-to-peer sync, so the others should be considered
untested):

1. Storing locally
2. Storing remotely in Amazon's S3
3. Storing remotely via SCP
4. Storing remotely in atmos

Depending on the option chosen, add a relevant subset of the following
to your repository's ``.git/config``::

    [git-media]

      transport = <scp|local|s3|atmos>

      # settings for local transport
      path = <local_filesystem_path>

      # settings for scp transport
      scpuser = <user>
      scphost = <host>
      scppath = <path_on_remote_server>

      # settings for s3 transport
      s3bucket = <name_of_bucket>
      s3key    = <s3 access key>
      s3secret = <s3 secret key>

      # settings for atmos transport
      endpoint = <atmos server>
      uid      = <atmos_uid>
      secret   = <atmos secret key>
      tag      = <atmos object tag>



Usage
--------------------

Summary: use ``git media sync`` after clone, push, pull.

Sharing your work
~~~~~~~~~~~~~~~~~~~~

Use your usual git workflow, eg::

        $ git add newbigfile.mov
        $ git commit -m "Something something."
        $ git push

Then upload your new/changed media::

        $ git media sync

Getting other people's work
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use your usual git workflow, eg::

        $ git pull

Then get new/changed media::

        $ git media sync    


More
~~~~~~~~~~~~~~~~~~~~

You can check the status of your media files via::

	$ git media status


Release notes
--------------------

0.1.2.cb.1
~~~~~~~~~~~~~~~~~~~~

* Fixed handling of line endings in smudge and clean filters
* Fixed finding references on Windows

A full list of changes since the previous release is available 
`on GitHub <https://github.com/ceball/git-media/compare/v0.1.2.cb.0...v0.1.2.cb.1>`_.

0.1.2.cb.0
~~~~~~~~~~~~~~~~~~~~

* Initial (unreleased) version; no code changes from https://github.com/schacon/git-media/commit/42abe20ab95dd18e3f5e83fb190142abd36f2e3e

Copyright
--------------------

Original work: Copyright (c) 2009 Scott Chacon. See LICENSE for details.

Modified work: All changes from the original work are in the public
domain, provided as-is, with no warranty of any kind expressed or
implied.  Anyone is free to copy, modify, publish, use, compile, sell,
or distribute the changes under any license, for any purpose,
commercial or non-commercial, and by any means.
