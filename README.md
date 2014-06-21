h1. Ext.ux.Exporter

h2. About

p. Exporter is a generic export class which takes any Ext.data.Store-based component (e.g. grids and similar) and exports the data in any format.

p. Exporter works both client-side and server-side(only if browser doesn't support client side save).  
p. It uses a Formatter class to generate a document (.xls, .csv etc) and then redirects the user's browser to a blob url so that they can view or download it.

h2. Installation

p. Download the latest version in any of the following ways:

* gitbub link https://github.com/yogeshpandey009/Ext.ux.Exporter/

h2. Usage

p. The most common use case for the Exporter is exporting a grid to CSV/Excel, which is as simple as doing the following:

bc.. var grid = new Ext.grid.GridPanel({
  store: someStore,
  tbar : [
    {
      xtype: 'exportbutton',
      store: someStore
	  //OR
	  //component: someGrid
	  //component: someTree
	  //component: '#someGridItemId'
    }
  ],
  //your normal grid config goes here
});

p. The provided 'exportbutton' is just a specialised Ext.Button subclass which uses Ext.ux.exporter.FileSaver.  

h2. Demo

https://fiddle.sencha.com/#fiddle/6st
