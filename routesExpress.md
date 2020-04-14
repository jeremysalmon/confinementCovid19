L'objectif est de structurer correctement les routes dans une app node.js :

## Système de loaders

On va mettre en place un système de loaders qui nous permettra de pouvoir facilement ajouter des traitements à faire lors du démarrage de l'application.

### Dans ```index.js``` : 

```await require('./loaders).default({expressApp: app})```

Ou ```app``` est une instance ```express()```

### Dans ```loaders/index.js```

```
import expressLoader from './express'

export default async ( { expressApp } ) => {
  await expressLoader( { app: expressApp } )
}
```

Dans l'exemple ci dessus, vous importez vos différents loader et executez de façon asynchrone

### Dans ```loaders/express.js```

```
import express from 'express'
import bodyParser from 'body-parser'
import cors from 'cors'
import routes from '../routes' // Vos routes
import config from '../config' // Les données de configuration. A adapter à votre structure

export default ({app} : {app: express.Application}) => {
  // Route pour gérer les applications de gestion de disponibilité
  app.get('/status', (req, res) => {
    res.status(200).end();
  });
  app.head('/status', (req, res) => {
    res.status(200).end();
  });
  
  app.enable('trust proxy'); // Si votre application est derrière un reverse-proxy
  
  app.use(cors()); // 
  
  app.use(bodyParser.json());
  
  /*
    Dans notre fichier de config nous avons un :
    api: {
      prefix: '/api',
    }
  */
  app.use(config.api.prefix, routes());
  
  app.use((req, res, next) => { // Gestion des 404
    const err = new Error('Not Found');
    err['status'] = 404;
    next(err);
  });
  
  app.use((err, req, res, next) => {
    //401 géré par express-jwt
    if (err.name === 'UnauthorizedError') {
      return res
        .status(err.status)
        .send({ message: err.message })
        .end();
    }
    return next(err);
  });
  
  app.use((err, req, res, next) => { // Erreur système
    res.status(err.status || 500);
    res.json({
      errors: {
        message: err.message,
      },
    });
  });

  
  
}
```
