# Deploy to azure setup, see NDNE-README.md at root

# Custom Theme

- Cloned the quickstart theme: https://docs.nodebb.org/development/themes/
- Located in the /ndne/nodebb-theme-ndne folder
- npm linked it:

```
cd /ndne/nodebb-theme-ndne
npm link
cd /../..
npm link nodebb-theme-ndne
```

- after updates run: `./nodebb build tpl`

- do i need to run this eventually for deploy?? : `npm install ndne/nodebb-theme-ndne`