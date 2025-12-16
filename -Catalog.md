---
cssclasses: catalog
date_created: 2025-12-03
publish: true
tags:
ddckey: 930
author: 
---
# Tables with dataviewjs
## Collect the pages
```dataviewjs
dv.el("b", "Just show");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
    ;
pages.forEach(p => {dv.el("p",p.file.path)});
```
### Description
First, collect the pages you are interested in array pages. The way you do it, is up to you. In all the following examples we will use same collection.
##  Show pages formatted
```dataviewjs
dv.el("b", "Formatted output");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
    ;
pages.forEach(p => {
  dv.el("p","[[" + p.file.path + "|" + p.file.name.slice(4) + "]]")
});
```
### Description
You can format the output. Here it will be shown as markdown link, with the filename without the first four characters as link name.
##  Use functions for the formatting
```dataviewjs
dv.el("b", "With functions formatted output");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

function filelink(file) {
  let fllink = "";
  fllink = "[[" + file.path + "|" + file.name.slice(4) + "]]"
  return fllink; 
}

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
    ;
pages.forEach(p => {
  dv.el("p",filelink(p.file))
});
```
### Description
Formatting can be done by a function.
## Show them in a table
```dataviewjs
dv.el("b", "simple table");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
     ;
 dv.table(["Filelink", "ddclong", "Media"], pages
  .sort(p => p.ddckey)   
  .map(p => [
    p.file.link,
    p.ddckey,
    p.media
  ])
);
```
### Description
The pages will be shown in a table with the function `dv.table`. This has two arguments: header_names and array_of_rows, where each row is an array of fields. `pages.map` gives us the rows and the mapping itself defines the columns which give us the fields.

`pages.map(p => [p.file.name])` runs on each page of pages. The variable name for the single page is `p`. 
## Format some of the fields in the table 
```dataviewjs
dv.el("b", "formatted fields");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
     ;
 dv.table(["Filelink", "ddclong", "Media"], pages
  .sort(p => p.ddckey)   
  .map(p => [
	"[[" + p.file.path + "|" + p.file.name.slice(4) + "]]",
    "DDC"+p.ddckey,
    p.media ? p.media.join() : ""
  ])
);
```
### Description
The fields can be formatted.
## Use functions for the formatting in table
```dataviewjs
dv.el("b", "functions for formatting");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
     ;
function filelink(file) {
  let fllink = "";
  fllink = "[[" + file.path + "|" + file.name.slice(4) + "]]"
  return fllink; 
}
function ddclong(ddc) {
  let ddclong = "";
  ddclong = "DDC" + ddc
  return ddclong; 
}
function media(m) {
  return m ? m.join(", ") : "";
}

 dv.table(["Filelink", "ddclong", "Media"], pages
  .sort(p => p.ddckey)   
  .map(p => [
	filelink(p.file),
    ddclong(p.ddckey),
    media(p.media)
  ])
);
```
### Description
Formatting can be done by functions.
## Exkurs: Show first heading, not the file name
```dataviewjs
dv.el("b", "Formatted output");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

function filelink(file,noteText) {
  let fllink = "";
  let regex = /## .*/;
  let found = noteText.match(regex);
  let headerName = file.path;
  if (found != null) {
    headerName = found[0].slice(3).slice(0,len);
  }
  fllink = "[[" + file.path + "|" + headerName + "]]"
  return fllink; 
}

const pages = dv
    .pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth)
     )
    ;
pages.forEach(p => {
  dv.io.load(p.file.path).then(result =>
    dv.el("p",filelink(p.file, result))
  )
});
```
### Description
This part is nearly as [[-Catalog#Use functions for the formatting]], but first heading will replace the filename. Unfortunately, to get the heading one has to parse the whole file content. To get the content one has to call `dv.io.load`, which is an asynchronous function. As soon as `dv.io.load` is ready, `.then` gives the result and with this output is formatted.
## Putting it all together
```dataviewjs
dv.el("b", "Putting all together");
const len = 60;
const eval_foldername = `"${dv.current().file.folder}"`;
const foldername = dv.current().file.folder;
function get_depth(path) { return path.split("/").length; }
const depth = get_depth(foldername);

function filelink(file,noteText) {
  let fllink = "";
  let regex = /## .*/;
  let found = noteText.match(regex);
  let headerName = file.path;
  if (found != null) {
    headerName = found[0].slice(3).slice(0,len);
  }
  fllink = "[[" + file.path + "|" + headerName + "]]"
  return fllink; 
}
function ddclong(ddc) {
  let ddclong = "";
  ddclong = "DDC" + ddc
  return ddclong; 
}
function media(m) {
  return m ? m.join(", ") : "";
}

const asypages = await Promise.all(
    dv.pages(eval_foldername)
    .where(p => (p.file.name.substring(0,1) != "-") 
             && (get_depth(p.file.folder)==depth) 
     )
	.map(async (page) => {
	   const content = await dv.io.load(page.file.path);
	   return {
	      fl: filelink(page.file, content),
		  ddckey: page.ddckey,
	      ddc: ddclong(page.ddckey),
	      mstr: media(page.media)
	   };
	})
);

dv.table(["Filelink", "ddclong", "Media"], asypages
  .sort((a,b) => a.ddckey - b.ddckey)   
  .map(asyp => [
    asyp.fl, 
    asyp.ddc, 
    asyp.mstr
  ])
);
```
### Description
The asynchronous call for the content now is done while getting the pages. It returns another object than `dv.pages`, so the result is named `asypages`.

Only in the asynchronous part one can access the file content. So formatting a field which uses file content has to be done in the asynchronous part. The formatting functions are called from within it. The formatted values are returned.

## Extra Plus
```dataviewjs
await dv.executeJs(await dv.io.load("catalog.js"));
```
### Description
The code can be written in a java script file. This file can be loaded and executed. So same query can be used to construct same table on different pages.