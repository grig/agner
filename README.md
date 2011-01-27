Agner
=====

Agner is a rebar-friendly Erlang package index inspired by Clojars and
Homebrew.

Essentially, Agner is an index of Erlang packages with some extra
capabilities such as versioning, downloads and so on.

Agner is a shorthand for *A Giant Nebula of Erlang Repositories*. It
also pays homage to the Danish statistician "Agner Krarup Erlang".

Motivation
----------

By now, there is a large set of Erlang tools and libraries out there,
all of them highly useful. The problem however is to provide an index
of these packages, so other people

   * Know of their existence
   * Can easily use a package in their own projects

Agner aims to provide such an index, by focusing on a number of
points:

* The index is loose in the sense that anyone can overlay the index
  and add their own packages to the repository
* The tool is as simple as possible, utilizing git (for the time
  being) to maintain the indexes
* Recognize the ideas of simplicity Joe Armstrong had in mind on
  the erlang-questions@ mailing list the
  [22-Jul-2010](http://www.erlang.org/cgi-bin/ezmlm-cgi?4:mss:52415:201007:npnohnblfemjooohecnk)

Use
===

This section introduces the terminology of Agner:

* **index/indices:** Where Agner finds its package index. Usually this
  is a github user with one or more packages among the users git
  repositories.
* **package:** A separate library or program indentified by the
  index. It is a `.agner` repository underneath the index-user, so one
  example would be `agner/gproc.agner` specifying a package for the
  `gproc` library undernath the `agner`-user.
* **project:** A software project, program or library, that contains
  the actual source code for the program or library. In the example,
  this is `esl/gproc` on github.
* **release:** A release of a package signifying a point in time
  where the package was deemed to be in a certain state. Is usually
  used when a new version of the software is released to the
  general public so you can refer to package X version Y
* **flavour:** A moving target of a package with some specified
  behaviour. It is used for tracking the development of a package
  over time. Common flavours include the *@master* flavour, used to
  track the development branch of a package and the *@release*
  flavour, used to track the latest release of the package.


Package organization
--------------------

When Agner is invoked, it will scan its *indices* for package
lists. The default index is "agner", which is located at
[https://github.com/agner/](https://github.com/agner/). The index is
scanned by looking for *Agner repositories* which are normal (github)
repositories suffixed with `.agner`. An example is the repository
[https://github.com/agner/getopt.agner](https://github.com/agner/getopt.agner)
which contains the package details of the `getopt` package.

It is important to nail down that there are three balls in the air:

* The index user, who has a list of
* `.agner` repositories, which points to
* Erlang software projects

By making a split between the repository containing the project and
the repository containing the package, we make it easy to identify
`.agner` repositories, and we enable a simple way to make the project
live in another source control system, for instance Mercurial (hg). It
is also way easier to keep the (small) `.agner` repositories in an
index and in the long run, it provisions for local caching.

Further indices can be added to Agner through the environment (TODO:
flesh out how that is done). Indices are searched
in the order of specification, allowing for overriding of a given
index. This allows you to create local indices or special indices for
your own use, or try something out on top of other indices.

The multiple indices approach solves authorization questions by solving it
"the git way". You put trust in the indices you add to Agner, so if
you don't trust an index, you can simply refrain from adding it. The
main "agner" index is intended to be the official source, but we
recognize that individuals might have reasons to overlay another index
on top. By having a loose index-construction, we hope to alleviate
some of the problems with access rights.

Package names
-------------

Package name is just either a package name such as
<code>mochiweb</code>, or (in case of github indices, it might also
take a form of <code>account/package</code>, for example
<code>yrashk/misultin</code>). We use package names to identify a
given package in Agner - but versions of the package is naturally not
part of its name. This allows for packages to exist in multiple
versions at a time.

Versions
--------

Agner has two kinds of versions:

* Release versions, normally something like <code>1.2.0</code>,
  represented using tags in `.agner` repos.
* Flavour versions, normally something like <code>@release</code>,
  represented using branches in `.agner` repos. Note the prefix of "@".

The intention is that a *release* version marks a given point in time
where a given version of the code base was released to the general
public. When Erlang/OTP is released as OTP-R14B01 for instance, it
signifies a *release* in Agner-terminology. On the other hand, a
*flavour* signifies a moving target. Continuing the OTP-R14B01
example from before, it would be natural to have a *@dev* flavour
which tracks the Erlang/OTP branch called `dev`. The other important
flavour is *@release* which will track the latest release.

### How to create relases and flavours

As hinted, a release version is a *tag* in a `.agner` repository. So
to create a release, you alter the `.agner` repository to match your
liking and then you tag it (with a standard `git tag` command
invocation). Agner will now pick up the change.

Likewise, for a flavour version, you *branch* the `.agner` repository
and alter the branch so it does what your flavour intended to
do. Flavours can be made for anything you would like to track over
time. By default, the advice is to create two flavours, *@master* and
*@release* tracking, respectively, the current development of a
project and the latest stable release of that project.

Keeping everything up-to-date is now outsourced to git and you can use
usual git-commands to manipulate the `.agner` repository.

### The contents of an .agner package

The `.agner` package repository contains a file of Erlang-terms, called
`agner.config`. This file looks like this:

    {name, "etorrent"}.
    {authors, ["Jesper Louis Andersen <jesper.louis.andersen@gmail.com>"]}.
    {description, "Etorrent is a bittorrent client implementation in Erlang focusing on fault-tolerance"}.
    {homepage, "http://github.com/jlouis/etorrent"}.
    {rebar_compatible, true}.
    {license, "BSD2", "COPYING"}.
    {erlang_versions, [otp_r14b, otp_r14b01, otp_r13b04]}.
    {url, {git, "https://github.com/jlouis/etorrent.git", {branch, "master"}}}.

Or in a more generic way:

    {name, ProjectName}.
    {authors, [Author]}.
    {description, ProjectDescription}.
    {homepage, ProjectHomepage}.
    {rebar_compatible, IsRebarCompatible}.
    {license, LicenseType, LicenseFile}.
    {erlang_versions, [OTPAtom]}.
    {url, UrlSpec}.

* `ProjectName :: string()` - is the project name. This is usually
  named the same as the `.agner` package to minimize confusion.
* `[Author] :: [string()]` - Can really be any string, but it is
  usually the names of the project authors in a list including their
  email-addresses for easy contact.
* `ProjectDescription :: string()` - A description of the
  project. Used for searching through projects.
* `ProjectHomepage :: string()` - The URL of the homepage of the
  project.
* `IsRebarCompatible :: boolean()` - Set to `true` if this project
  uses `rebar`.
* `LicenseType :: string(), LicenseFile :: string()` - Two
  strings. The first one specifies the general license type of the
  project and the second string explains where the license is to be
  found from the top level directory (usually file-names like
  `COPYING` or `LICENSE` are used for this).
* `[OTPAtom] :: [otp_rXXb | otp_rXXbYY]` - A list of what OTP versions
  the project can be used with. the `XX` is a major release number in
  Erlang/OTP (12,13,14,...) and `YY` is a minor release number (01,
  02, ...).
* `UrlSpec :: {git, URL, GitSpec}` - Specifies where to fetch the
  project. `GitSpec` has type `sha1() | {tag, string()} | {branch, string()}`
  and points to either string-based sha1 representation, a git *tag* or a *git* branch
  respectively. Notice that you can't specify more than one target in
  this file. To handle multiple versions, you use *releases* and
  *flavours* by altering the `.agner` repository wherein this
  configuration file lies.

Commands
--------

    agner list [-d/--descriptions]

Will list all agner-packages. With the `-d` or `--descriptions`
option, it will also print out the descriptions of the packages, for
easy grepping to find relevant packages.

    agner spec PACKAGE [-v/--version package_version]

Will print a specification of a given package on stdout. If the
optional version constraint is given (for example `agner spec gproc -v
@release`) then the output is of that version. By default, the
`@master` flavour is chosen.

    agner fetch PACKAGE [DESTDIR] [-v/--version package_version]

Fetch a given `PACKAGE` to either the current directory or,
optionally, to the `DESTDIR` directory. The version constraint is as
were the case for `agner spec`.

    agner versions PACKAGE

List the versions of the given `PACKAGE`


Rebar
-----

Agner-compatible rebar is available at
[agner branch](https://github.com/agner/rebar/tree/agner) of
[agner/rebar](https://github.com/agner/rebar). Or you can download
ready-made rebar from
[agner itself](https://github.com/agner/agner/raw/master/rebar). We
hope to get rebar integration in the upstream with time.

Using it with rebar is fairly simple, it uses rebar's deps feature:

    {deps, [
              {typespecs, "0.1", {agner, "typespecs"}},
              {getopt, "0.3.0", {agner, "getopt"}}
           ]}.

You can also specify your own indices:

    {agner_indices, [{github, "yourgithubusername"},{github,"agner"}].

Contributing
------------

Please read at the [wiki](https://github.com/agner/agner/wiki/Contributing).
