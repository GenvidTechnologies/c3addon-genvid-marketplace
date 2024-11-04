=================================
Genvid Construct 3 Addon Template
=================================

## To use

Explain the API here.

## To develop

```bash
npx http-server src --cors
```

Because of the restricted CSP in Construct3, make sure to use http://localhost:8080/addon.json instead of 127.0.0.1.

## To build

```bash
cd src
7z a -tzip -r ..\template-addon.c3addon .
cd -
```
