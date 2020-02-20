## 随机mock数据简介

通过解析swagger yaml结构，根据schema内容mock随机数据。

## 实际操作

#####组件

* swagger-express-middleware
* swagger-mock-parser

##### 如何使用

将下面的源代码copy到新js文件中（例如：mock_data.js）。修改源代码中的`PetStore.yaml`文件名为自己的spec文件。然后执行命令

```shell
node mock_data.js
```

启动成功，即可使用随机mock方式。

```javascript
/**************************************************************************************************
 * This sample demonstrates the most simplistic usage of Swagger-Express-Middleware.
 * It simply creates a new Express Application and adds all of the Swagger middleware
 * without changing any options, and without adding any custom middleware.
 **************************************************************************************************/
'use strict';

const createMiddleware = require('swagger-express-middleware');
const _ = require("lodash");
const path = require('path');
const express = require('express');
const typeIs = require("type-is");
const SemanticResponse = require("swagger-express-middleware/lib/mock/semantic-response");
const util = require("swagger-express-middleware/lib/helpers/util");
const JsonSchema = require("swagger-express-middleware/lib/helpers/json-schema");
const Parser = require('swagger-mock-parser');

// Create an Express app
const app = express();

// Initialize Swagger Express Middleware with our Swagger file
let swaggerFile = path.join(__dirname, 'PetStore.yaml');
createMiddleware(swaggerFile, app, (err, middleware) => {

  // Add all the Swagger Express Middleware, or just the ones you need.
  // NOTE: Some of these accept optional options (omitted here for brevity)
  app.use(
    middleware.metadata(),
    middleware.CORS(),
    middleware.files(),
    middleware.parseRequest(),
    middleware.validateRequest(),
    // middleware.mock()
  );
  
      app.get('/*', function(req, res, next){
          console.log('visit / ...');
          // console.log(JSON.stringify(res.req.swagger, null, 2));
          const resSwagger = mock(req, res, next);
          res.swagger = resSwagger;
          if (res.swagger) {
              console.log('swagger不为空');
              console.log(JSON.stringify(res.swagger));
              mockResponseBody(req, res, next);
          }
          //res.send('oookkk');
    });

  // Start the app
  app.listen(8000, () => {
    console.log('The Swagger Pet Store is now running at http://localhost:8000');
  });
});

function mock(req, res, next) {
    if (util.isSwaggerRequest(req)) {
      let response;

      // Is there already a statusCode? (perhaps set by third-party middleware)
      if (res.statusCode && req.swagger.operation.responses[res.statusCode]) {
        util.debug("Using %s response for %s %s", res.statusCode, req.method, req.path);
        response = req.swagger.operation.responses[res.statusCode];
      }
      else {
        // Use the first response with a 2XX or 3XX code (or "default")
        let responses = util.getResponsesBetween(req.swagger.operation, 200, 399);

        if (responses.length > 0) {
          response = responses[0].api;

          // Set the response status code
          if (_.isNumber(responses[0].code)) {
            util.debug("Using %s response for %s %s", responses[0].code, req.method, req.path);
            res.status(responses[0].code);
          }
          else {
            if (req.method === "POST" || req.method === "PUT") {
              res.status(201);
            }
            else if (req.method === "DELETE" && !responses[0].api.schema) {
              res.status(204);
            }
            else {
              res.status(200);
            }
            util.debug("Using %s (%d) response for %s %s", responses[0].code, res.statusCode, req.method, req.path);
          }
        }
        else {
          // There is no response with a 2XX or 3XX code, so just use the first one
          let keys = Object.keys(req.swagger.operation.responses);
          util.debug("Using %s response for %s %s", keys[0], req.method, req.path);
          response = req.swagger.operation.responses[keys[0]];
          res.status(parseInt(keys[0]));
        }
      }

      // The rest of the Mock middleware will use this ResponseMetadata object
      res.swagger = new SemanticResponse(response, req.swagger.path);
      
      return res.swagger;
    }
}

function mockResponseBody(req, res, next) {
  if (res.swagger) {
    if (res.swagger.isEmpty) {
      // There is no response schema, so send an empty response
      util.debug('%s %s does not have a response schema. Sending an empty response', req.method, req.path);
      res.send();
    }
    else {
      // Serialize the response body according to the response schema
      let schema = new JsonSchema(res.swagger.schema);
      let serialized = schema.serialize(res.swagger.wrap(res.body));
      let parser = new Parser();
      console.log('res.swagger.schema.type', res.swagger.schema.type);
      switch (res.swagger.schema.type) {
        case 'object':
        case 'array':
          // return defined example otherwise use mock parser return random data
          if (res.swagger.schema.example) {
            sendObject(req, res, next, res.swagger.schema.example);
          } else {
            sendObject(req, res, next, parser.parse(res.swagger.schema));
          }
          break;
        case undefined:
          sendObject(req, res, next, serialized);
          break;

        case 'file':
          sendFile(req, res, next, serialized);
          break;

        default:
          sendText(req, res, next, serialized);
      }
    }
  }
}

function sendObject(req, res, next, obj) {
  setContentType(req, res, ['json', '*/json', '+json']);

  util.debug('Serializing the response as JSON');
  console.log('sendObject obj', JSON.stringify(obj));
  res.json(obj);
}

function sendText(req, res, next, data) {
  setContentType(req, res,
    ['text', 'html', 'text/*', 'application/*'],                // allow these types
    ['json', '*/json', '+json', 'application/octet-stream']);   // don't allow these types

  util.debug('Serializing the response as a string');
  res.send(_(data).toString());
}

function sendFile(req, res, next, file) {
  if (file instanceof Buffer) {
    setContentType(req, res, ['application/octet-stream', '*/*']);

    // `file` is the file's contents
    util.debug('Sending raw buffer data');
    res.send(file);
    return;
  }

  // `file` is a file info object
  fs.exists(file.path || '', function(exists) {
    if (exists) {
      var isAttachment = _.some(res.swagger.headers, function(header, name) {
        return name.toLowerCase() === 'content-disposition';
      });

      if (isAttachment) {
        // Get the filename from the "Content-Disposition" header,
        // or fallback to the request path
        let fileName = /filename\=\"(.+)\"/.exec(res.get('content-disposition'));
        fileName = fileName ? fileName[1] : req.path;

        util.debug('Sending "%s" as an attachment named "%s"', file.path, fileName);
        res.download(file.path, fileName);
      }
      else {
        util.debug('Sending "%s" contents', file.path);
        res.sendFile(file.path, {lastModified: false});
      }
    }
    else {
      // Throw an HTTP 410 (Gone)
      util.debug('Unable to find the file: "%s".  Sending an HTTP 410 (Gone)', file.path);
      next(ono({status: 410}, '%s no longer exists', file.path || req.path));
    }
  });
}

function setContentType(req, res, supported, excluded) {
  // Get the MIME types that this operation produces
  let produces = req.swagger.operation.produces || req.swagger.api.produces || [];

  if (produces.length === 0) {
    // No MIME types were specified, so just use the first one
    util.debug('No "produces" MIME types are specified in the Swagger API, so defaulting to %s', supported[0]);
    res.type(supported[0]);
  }
  else {
    // Find the first MIME type that we support
    let match = _.find(produces, function(mimeType) {
      return typeIs.is(mimeType, supported) &&
        (!excluded || !typeIs.is(mimeType, excluded));
    });

    if (match) {
      util.debug('Using %s MIME type, which is allowed by the Swagger API', match);
      res.type(match);
    }
    else {
      util.warn(
        'WARNING! %s %s produces the MIME types that are not supported (%s). Using "%s" instead',
        req.method, req.path, produces.join(', '), supported[0]
      );
      res.type(supported[0]);
    }
  }
}

```



##注意

1. ==随机mock的结果数据是根据schema的type决定的。==

2. type如果没有在yaml写，是不能决定mock什么类型的数据。

3. array结果数据，只有第一条是完整，其余只有一个属性结果。**可能是有bug**
4. 涉及到数字的结果，会带有负号（-），不完美。