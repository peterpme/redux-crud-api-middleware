# redux-crud-api-middleware
Redux Crud Api Middleware

## Proposal

Given a a model or entity, generate a CRUD api to handle those requests.

Example:

```es6
const PRODUCTS_URL = '/api/products'
const api = crudApi('PRODUCT', PRODUCTS_URL)

const createProduct = (body) => api.create(body)
const fetchProducts = () => api.fetch()
const fetchProduct  = (id) => api.fetch(id)
const updateProduct = (id, body) => api.update(id, body)
const deleteProduct = (id) => api.delete(id)
```

Would generate the following routes/actions:

- create (POST) `/api/products`
- read (GET) `/api/products` and `/api/products/{id}`
- update (PUT/PATCH) `/api/products/{id}`
- delete (DELETE) `/api/products/{id}`

```es6
const CREATE_PRODUCT_REQUEST = 'CREATE_PRODUCT_REQUEST'
const CREATE_PRODUCT_SUCCESS = 'CREATE_PRODUCT_SUCCESS'
const CREATE_PRODUCT_FAILURE = 'CREATE_PRODUCT_FAILURE'

const GET_PRODUCTS_REQUEST   = 'GET_PRODUCTS_REQUEST'
const GET_PRODUCTS_SUCCESS   = 'GET_PRODUCTS_SUCCESS'
const GET_PRODUCTS_FAILURE   = 'GET_PRODUCTS_FAILURE'

const GET_PRODUCT_REQUEST   = 'GET_PRODUCT_REQUEST'
const GET_PRODUCT_SUCCESS   = 'GET_PRODUCT_SUCCESS'
const GET_PRODUCT_FAILURE   = 'GET_PRODUCT_FAILURE'

const UPDATE_PRODUCT_REQUEST = 'UPDATE_PRODUCT_REQUEST'
const UPDATE_PRODUCT_SUCCESS = 'UPDATE_PRODUCT_SUCCESS'
const UPDATE_PRODUCT_FAILURE = 'UPDATE_PRODUCT_FAILURE'

const DELETE_PRODUCT_REQUEST = 'DELETE_PRODUCT_REQUEST'
const DELETE_PRODUCT_SUCCESS = 'DELETE_PRODUCT_SUCCESS'
const DELETE_PRODUCT_FAILURE = 'DELETE_PRODUCT_FAILURE'
```

The api should be configurable to handle different scenarios and different libraries. `Fetch` or `axios` would be a good first project.

Possible `crudApi` implementation (with some holes)

```es6

class CrudApi {
  constructor(entity, endpoint, xhr) {
    this.entity = entity;
    this.endpoint = endpoint;
    this.xhr = xhr;
  }
  
  create() {
    return this.sendRequest('POST', url, query, body)
  }
  
  fetch(id) {
    return this.sendRequest('GET', url, query, body)
  }
  
  update(id, body) {
    return this.sendRequest('PUT', url, query, body)
  }
  
  delete(id) {
    return this.sendRequest('DELETE', url, query, body)
  }
  
  handleResponse(err, payload) {
    if (err) return this.handleError(err, payload)
    return resolve(payload)
  }
  
  handleError(err, payload) {
    if (err.status === 401) this.handleUnauthorized()
    return reject(err, payload)
  }
  
  sendRequest(method, url, query, body, headers, host) {
  
  const xhr = request(method, url)
  
    return new Promise((resolve, reject) => {
      if (headers) xhr.set(headers)
      if (body) xhr.send(body)
      if (query) xhr.query(query)
      
      xhr.end((err, payload) => this.handleResponse(err, payload))
    })
    
  }
  
}

```
