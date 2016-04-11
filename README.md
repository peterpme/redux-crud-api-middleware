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
const CREATE_PRODUCT_RESET   = 'CREATE_PRODUCT_RESET'

const GET_PRODUCTS_REQUEST   = 'GET_PRODUCTS_REQUEST'
const GET_PRODUCTS_SUCCESS   = 'GET_PRODUCTS_SUCCESS'
const GET_PRODUCTS_FAILURE   = 'GET_PRODUCTS_FAILURE'
const GET_PRODUCTS_RESET     = 'GET_PRODUCTS_RESET'

const UPDATE_PRODUCT_REQUEST = 'UPDATE_PRODUCT_REQUEST'
const UPDATE_PRODUCT_SUCCESS = 'UPDATE_PRODUCT_SUCCESS'
const UPDATE_PRODUCT_FAILURE = 'UPDATE_PRODUCT_FAILURE'
const UPDATE_PRODUCT_RESET   = 'UPDATE_PRODUCT_RESET'

const DELETE_PRODUCT_REQUEST = 'DELETE_PRODUCT_REQUEST'
const DELETE_PRODUCT_SUCCESS = 'DELETE_PRODUCT_SUCCESS'
const DELETE_PRODUCT_FAILURE = 'DELETE_PRODUCT_FAILURE'
const DELETE_PRODUCT_RESET   = 'DELETE_PRODUCT_RESET'
```

The api should be configurable to handle different scenarios and different libraries. `Fetch` or `axios` would be a good first project.

The api could look similar to this:

```es6
export function createProduct (product) {
  return {
    [CALL_API]: {
      endpoint: `${PRODUCTS_URL}`,
      method: 'POST',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      },
      credentials: 'same-origin',
      body: JSON.stringify(product),
      types: [
        CREATE_PRODUCT_REQUEST,
        {
          type: CREATE_PRODUCT_SUCCESS,
          payload: (action, state, res) => {
            const reqBody = JSON.parse(action[CALL_API].body)
            const _id = res.headers.get('location').split('/').pop()
            const statusCode = res.status
            const statusText = res.statusText
            const body = {
              _id,
              ...reqBody
            }
            return {
              body,
              statusCode,
              statusText
            }
          }
        },
        CREATE_PRODUCT_FAILURE
      ]
    }
  }
}
```

