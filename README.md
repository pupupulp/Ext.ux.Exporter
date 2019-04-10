# Ext.ux.Exporter

Exporter is a generic export class which takes any Ext.data.Store-based component (e.g. grids and similar) and exports the data in any format.

Exporter can work both client-side and server-side(only if browser doesn't support client side save).

It uses a Formatter classes to generate a document (.xls, .csv etc) which is then used to create a Blob. With this blob url you can save file locally in browser.

If browser doesn't support blob, you can either open document in new window with data: uri or download remotely by sending data to server.

## Quickstart

### Installation

```cli
$ git clone https://github.com/pupupulp/Ext.ux.Exporter.git
```

### Usage

@app.js

```javascript
Ext.Loader.setConfig({
    enabled: true,
    disableCaching: false,
    paths: {
        'Ext.ux.exporter': 'parh/to/cloned/repo'
    }
});
```

@view.js

```javascript
{
    xtype: 'grid',
    store: store,
    columns: [{}],
    tbar: [
        {
            xtype: 'exporterbutton',
            reference: 'export-csv',
            text: 'Export (csv)',
        },
        {
            xtype: 'exporterbutton',
            reference: 'export-excel',
            text: 'Export (xls)',
            title: 'Export Template XLS',
            format: 'excel'
        }
    ],
    listeners: {
        select: 'updateExportStore'
    }
}
```

**@Controller.js (Optional)**
- For handling of exporting of grid selections

```javascript
// via ExtJS
updateExportStore: function(grid, record) {
    var records = Ext.pluck(grid.getSelection(), 'data');

    var keys = Object.keys(records[0]);

    var store = Ext.create('Ext.data.Store', {
			fields: keys,
			data: records
        });

    var exportCsv = Ext.ComponentQuery.query(`button[reference = export-csv]`)[0],
        exportExcel = Ext.ComponentQuery.query(`button[reference = export-excel]`)[0];

    exportCsv.store = store;
    exportExcel.store = store;
}

// via KonyvtarJS
updateExportStore: function(grid, record) {
    var records = Ext.pluck(grid.getSelection(), 'data');

    var store = Kony.store.local(records);

    var exportCsv = Kony.component.reference('button', 'export-csv'),
        exportExcel = Kony.component.reference('button', 'export-excel');

    exportCsv.store = store;
    exportExcel.store = store;
}
```

_The provided 'exporterbutton' is just a specialised Ext.Button subclass which uses Ext.ux.exporter.FileSaver to save file locally or download remotely._


### Demo (by yogeshpandey009)

https://fiddle.sencha.com/#fiddle/6st

## Documentation

### Modifications

**@ExporterButton.js**

- Added `me.preConfig`

```javascript
constructor: function(config) {
    var me = this;

    me.preConfig = config;

    Ext.ux.exporter.ExporterButton.superclass.constructor.call(me, config);

    me.on("afterrender", function() { //wait for the button to be rendered, so we can look up to grab the component
        if (me.component) {
            me.component = !Ext.isString(me.component) ? me.component : Ext.ComponentQuery.query(me.component)[0];
        }
        me.setComponent(me.store || me.component || me.up("gridpanel") || me.up("treepanel"), config);
    });
},
```

- Revised parameters of `Ext.ux.exporter.Exporter.exportAny`

```javascript
onClick: function(e) {
    var me = this,
        blobURL = "",
        format = me.format,
        title = me.title,
        remote = me.remote,
        dt = new Date(),
        link = me.el.dom,
        res, fullname;

    me.fireEvent('start', me);
    res = Ext.ux.exporter.Exporter.exportAny(me.component, me.store, format, { title : title });
    filename = title + "_" + Ext.Date.format(dt, "Y-m-d h:i:s") + "." + res.ext;
    Ext.ux.exporter.FileSaver.saveAs(res.data, res.mimeType, res.charset, filename, link, remote, me.onComplete, me);

    me.callParent(arguments);
},
```

**@Exporter.js**

- Revised parameter and return statement of `exportAny`:

```javascript
exportAny: function(component, store, format, config) {
    var func = "export";
    if (!component.is) {
        func = func + "Store";
    } else if (component.is("gridpanel")) {
        func = func + "Grid";
    } else if (component.is("treepanel")) {
        func = func + "Tree";
    } else {
        func = func + "Store";
        component = component.getStore();
    }
    var formatter = this.getFormatterByName(format);
    return this[func](component, store, formatter, config);
},
```

- Revised the parameters of the following functions

```javascript
exportGrid: function(grid, store, formatter, config);
exportStore: function(compomemt, store, formatter, config);
exportTree: function(tree, store, formatter, config);
```

## About

### Contributing

Pull requests and stars are always welcome.

### Related Projects

You might want to checkout these projects:

- [yogeshpandey009/Ext.ux.Exporter](https://github.com/yogeshpandey009/Ext.ux.Exporter) - A fork of edspencer/Ext.ux.Exporter, Exports an Ext.data.Store to Excel or CSV.
- [edspencer/Ext.ux.Exporter](https://github.com/edspencer/Ext.ux.Exporter) - Exports an Ext.data.Store to Excel or CSV.
- [KonyvtarJS](https://github.com/pupupulp/konyvtar-js) - An opensource library/package of code wrappers for ExtJS 6.2.0 GPL.

### License

Copyright © 2014, [Ed Spencer](https://github.com/edspencer). Release under the [MIT License](https://github.com/pupupulp/Ext.ux.Exporter/blob/master/LICENSE.txt)