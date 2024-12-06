# create-torrent
![tests](https://github.com/bicycle-codes/crypto-util/actions/workflows/nodejs.yml/badge.svg)
[![types](https://img.shields.io/npm/types/@substrate-system/icons?style=flat-square)](README.md)
[![module](https://img.shields.io/badge/module-ESM%2FCJS-blue?style=flat-square)](README.md)
[![semantic versioning](https://img.shields.io/badge/semver-2.0.0-blue?logo=semver&style=flat-square)](https://semver.org/)
[![Common Changelog](https://nichoth.github.io/badge/common-changelog.svg)](./CHANGELOG.md)
[![install size](https://flat.badgen.net/packagephobia/install/@nichoth/session-cookie)](https://packagephobia.com/result?p=@nichoth/session-cookie)
[![dependencies](https://img.shields.io/badge/dependencies-zero-brightgreen.svg?style=flat-square)](package.json)
[![license](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)

<details><summary><h2>Contents</h2></summary>
<!-- toc -->
</details>

## Create .torrent files

![creation](https://raw.githubusercontent.com/webtorrent/create-torrent/master/img.jpg)

This module is used by [WebTorrent](http://webtorrent.io)! This module works in node.js and the browser.

## install

```sh
npm i -S create-torrent
```

## usage

The simplest way to use `create-torrent` is like this:

```js
import createTorrent from 'create-torrent'
import fs from 'fs'

createTorrent('/path/to/folder', (err, torrent) => {
  if (!err) {
    // `torrent` is a Buffer with the contents of the new .torrent file
    fs.writeFile('my.torrent', torrent)
  }
})
```

A reasonable piece length (approx. 1024 pieces) will automatically be selected
for the .torrent file, or you can override it if you want a different size (See
API docs below).

## api

### `createTorrent(input, [opts], function callback (err, torrent) {})`

Create a new `.torrent` file.

`input` can be any of the following:

- path to the file or folder on filesystem (string)
- W3C [File](https://developer.mozilla.org/en-US/docs/Web/API/File) object (from an `<input>` or drag and drop)
- W3C [FileList](https://developer.mozilla.org/en-US/docs/Web/API/FileList) object (basically an array of `File` objects)
- Node [Buffer](http://nodejs.org/api/buffer.html) object
- Node [stream.Readable](http://nodejs.org/api/stream.html) object

Or, an **array of `string`, `File`, `Buffer`, or `stream.Readable` objects**.

`opts` is optional and allows you to set special settings on the produced .torrent file.

``` js
{
  name: String,             // name of the torrent (default = basename of `path`, or 1st file's name)
  comment: String,          // free-form textual comments of the author
  createdBy: String,        // name and version of program used to create torrent
  creationDate: Date        // creation time in UNIX epoch format (default = now)
  filterJunkFiles: Boolean, // remove hidden and other junk files? (default = true)
  private: Boolean,         // is this a private .torrent? (default = false)
  pieceLength: Number,      // force a custom piece length (number of bytes)
  announceList: [[String]], // custom trackers (array of arrays of strings) (see [bep12](http://www.bittorrent.org/beps/bep_0012.html))
  urlList: [String],        // web seed urls (see [bep19](http://www.bittorrent.org/beps/bep_0019.html))
  info: Object,             // add non-standard info dict entries, e.g. info.source, a convention for cross-seeding
  onProgress: Function      // called with the number of bytes hashed and estimated total size after every piece
}
```

If `announceList` is omitted, the following trackers will be included automatically:

- udp://tracker.leechers-paradise.org:6969
- udp://tracker.coppersurfer.tk:6969
- udp://tracker.opentrackr.org:1337
- udp://explodie.org:6969
- udp://tracker.empire-js.us:1337
- wss://tracker.btorrent.xyz
- wss://tracker.openwebtorrent.com
- wss://tracker.webtorrent.dev

Trackers that start with `wss://` are for WebRTC peers. See
[WebTorrent](https://webtorrent.io) to learn more.

`callback` is called with an error and a Buffer of the torrent data. It is up to you to
save it to a file if that's what you want to do.

>
> [!NOTE]  
> Every torrent is required to have a name. If one is not explicitly provided
> through `opts.name`, one will be determined automatically using the following
> logic:
>

- If all files share a common path prefix, that will be used. For example, if all file
  paths start with `/imgs/` the torrent name will be `imgs`.
- Otherwise, the first file that has a name will determine the torrent name. For example,
  if the first file is `/foo/bar/baz.txt`, the torrent name will be `baz.txt`.
- If no files have names (say that all files are Buffer or Stream objects), then a name
  like "Unnamed Torrent <id>" will be generated.

>
> [!NOTE]  
> Every file is required to have a name. For filesystem paths or W3C File objects,
> the name is included in the object. For Buffer or Readable stream types, a `name` property
> can be set on the object, like this:
> 

```js
const buf = Buffer.from('Some file content')
buf.name = 'Some file name'
```

## command line

```
usage: create-torrent <directory OR file> {-o outfile.torrent}

Create a torrent file from a directory or file.

If an output file isn\'t specified with `-o`, the torrent file will be
written to stdout.
```

## license

MIT. Copyright (c) [Feross Aboukhadijeh](https://feross.org) and [WebTorrent, LLC](https://webtorrent.io).
