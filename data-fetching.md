STAR leverages [`react-query`](https://tanstack.com/query/latest/docs/vue/overview) to handle data-fetching. Which provides many features on fetching, caching, CRUD operations support, stale-while-revalidate mechanism, race conditions handling, etc.

### Data Fetching

#### HTTP Client

STAR leverages `axios` as a client to send HTTP requests because of the needs of an abstraction layer for HTTP traffic. `axios` offers many features that we should consider when evaluating a HTTP client on both the server side and the client side.

1. _Tree-shakable_: `axios` can't be tree-shaken.
2. _Size_: `axios` has a large size of `11KB` after minified and gziped.
3. _Interceptor_: `axios` offers an abstraction on intercepting requests and responses.
4. _Automatic error handling_: `axios` will automatically throw when an non-200 status code is returned. Which is useless for ous because our API always return `200`.
5. _Automatic request encoding_: `axios` automatically encode js object into `www-form-data`.
6. _Isomorphism_: `axios` offers an isomorphic API for both server side and client side, which is good for SSR.

> STAR leverages interceptors to automatically transform the JSON response from API into camel case, and the `www-form` request body into snake case for server. Both of these are deeply transformation.
>
> That means if the server returned a JSON like this:
>
> ```
> {
>   "photo_urls": ["content_url"],
>   time: {
>       "ship_date": "2024-01-21T11:21:48.709Z",
>   }
> }
> ```
>
> It will be converted into:
>
> ```
> {
>   "photoUrls": ["content_url"],
>   time: {
>       "shipDate": "2024-01-21T11:21:48.709Z",
>   }
> }
> ```

`@/services/axios` offers an `axios` instance called `$http` (convention from angular), which can be used for HTTP request.

Import it from `@/services` if your module isn't in that directory, like from `@/components`:

```js
import { $http } from "@/services";

$http
  .get()
  .then((_) => _.data)
  .catch();
```

Import it from `@/services/axios` if your module is inside `@/services`, like from `@/services/api`:

```js
import { $http } from "@/services/axios";

...
```

#### Data fetching effect on components

Say `https://petstore.swagger.io/v2/pet` is the API end point for our service. And we want to fetch it when our component mounted. Assume a `id` of pet is passed from `props`. We can do something like:

```js
// script setup
import { useQuery } from "vue-query";
import { $http } from "@/services";

const petQuery = (id: number) =>
  $http.get(`https://petstore.swagger.io/v2/pet/${id}`).then((_) => _.data);
const usePetQuery = (id: number) => useQuery(["pet", id], petQuery);
const { data: pet, isLoading, isError } = usePetQuery(props.id);
```

```html
// template
<div v-if="isLoading">loading...</div>
<div v-if="isError">Sorry. something is wrong. 😥</div>
<div v-else>{{ pet }}</div>
```

> Notice that a custom logic is needed if error is not told from the HTTP status code. This is our case that we should check the `success` field of the response for error handling.

It is advised to create a query function (just a function that fetches the URL) and a custom `useQuery` hook for every end points.

Our `@/services/api` literally copied the api endpoints. That means if you want to fetch a URL `/pet`, a module `pet` in `@/services/api` should be created. That is `@/services/api/pet`, it could be a file or a folder, it just didn't matter as long as people can access it through `@/services/api` and `@/services/api/pet`.

```text
/services
|- api
    |- load-teacher-paper
    |- jwt
    |- load-meta
    .
    .
    .
```

#### Data models
Module `@/servies/models` has all the models for API responses, errors and common data types. 

#### Validation

As everything that across boundary should be validated. We should validate both the requests and responses in ideal. But honestly I didn't know how to validate the responses as they didn't seem to have a consistent schema under the hook. For now, we can only _type_ the API responses on typescript, and we can just validate our payload before sending it to the server.

STAR leverages [`zod`](https://zod.dev/) for validation, for example, we have a API `POST` `https://petstore.swagger.io/v2/store/order` that can create an order for a pet. It takes a body payload like:

```JSON
{
  "id": 0,
  "petId": 0,
  "quantity": 0,
  "shipDate": "2024-01-21T11:21:48.709Z",
  "status": "placed",
  "complete": true
}
```

Which can be modeled on `zod` like:

```typescript
import { z } from "zod";

const orderPayloadZ = z.object({
  id: z.number(),
  petId: z.number(),
  quantity: z.number(),
  shipDate: z.coerce.date(),
  status: z.enum(["placed", "approved", "delivered"]),
  complete: z.boolean(),
});
export type OrderPayload = z.infer<typeof orderPayloadZ>;
```

And we can validate the payload like:

```typescript
const orderQuery = (payload: OrderPayload = {}) =>
  $http.post("https://petstore.swagger.io/v2/store/order", {
    ...orderPayloadZ.parse(payload),
  });
```
If we send an invalid payload, the fetch function above will throw, Such that we can validate our payload before we actually send out the request. Which will help us a lot on debugging 😇.

> Notice that the `payload` have a default value of `{}`, which is useful for simple requests. `zod` will return the default for `.parse({})`. If you don't want to throw an error, or just want to deal with `undefined`, you can do `z.catch(() => "default-value")`, for example, `z.number().catch(() => 1)` will return `1` for `.parse(undefined)`.

### Caching
STAR leverages `react-query` to cache the API results within the application. In contrast, we didn't implemented a network layer caching, which usually done with a service worker.

STAR uses a *Stale-While-Revalidate* (SWR) strategy for data caching. By default, STAR only try to fetch when -
1. A new component mounted
2. Window is re-focused
3. Netwrok is re-connect
Otherwise, vue-query client will use the cached query for that fetch, this is called *deduping*.
