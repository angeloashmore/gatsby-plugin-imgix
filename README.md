# gatsby-plugin-imgix

Gatsby plugin which enables the use of [Imgix][imgix] to apply image
transformations at request-time. For most cases, this can replace the use of
`gatsby-transformer-sharp` and speed up your build times significantly.

Learn more about [Imgix on their site][imgix].

## Status

[![npm version](https://img.shields.io/npm/v/gatsby-plugin-imgix?style=flat-square)](https://www.npmjs.com/package/gatsby-plugin-imgix)

## Install

```sh
npm install --save gatsby-plugin-imgix
```

## How to use

```javascript
// gatsby-config.js

module.exports = {
  plugins: [
    {
      resolve: 'gatsby-plugin-imgix',
      options: {
        // Imgix source domain. This is required.
        domain: 'example-domain.imgix.net',

        // Imgix source secure URL token. Providing this will
        // automatically secure all of your image URLs. This is required if
        // your source type is a Web Proxy.
        // See: https://docs.imgix.com/setup/securing-images
        secureURLToken: process.env.IMGIX_SECURE_URL_TOKEN,

        // Imgix source type. If your source type is a Web Proxy, set this to
        // "webProxy". Otherwise, you may omit this field. URLs provided to the
        // plugin will automatically be converted to a Web Proxy-compatible URL
        // if set to "webProxy".
        sourceType: 'webProxy',

        // List of fields to copy into `fields` as Imgix images. You may list
        // as many fields as needed.
        fields: [
          {
            // The node type containing the image to copy.
            nodeType: 'MarkdownRemark',

            // Name of the new field to create. If you set this to
            // "featuredImage", a field at "fields.featuredImage" will be
            // created.
            fieldName: 'featuredImage',

            // Path to the URL in the node. One of `urlPath`, `getUrl`, or
            // `getUrls` may be provided to fetch a single URL or an array of
            // URLs depending on the option provided.
            urlPath: 'frontmatter.featured_image',
          },
        ],
      },
    },
  ],
}
```

## How to query

Fields setup in your configuration are copied into a `fields` property on the
image's node.

For example, a `ShopifyProduct` node with an image field name called
`featuredImage` could be queried like the following:

```graphql
query {
  allShopifyProduct {
    nodes {
      id
      fields {
        featuredImage {
          url
        }
      }
    }
  }
}
```

`fields` is a special Gatsby field reserved for plugins to add data to existing
nodes. If `fields` does not already exist on your node, it will be added
automatically.

See the [documentation for `createNodeField`][gatsby-createnodefield] for more
information.

### Query image URLs with transformations

**Not yet implemented**

### Query responsive images

Responsive images to display using [`gatsby-image`][gatsby-image] can be queried
using the `fixed` or `fluid` field and a GraphQL fragment. Use this pattern,
where `...ImageFragment` is one of the following fragments:

- GatsbyImgixFixed
- GatsbyImgixFixed_noBase64
- GatsbyImgixFluid
- GatsbyImgixFluid_noBase64

Learn about the different types of responsive images and fragments from
[`gatsby-image`’s official docs][gatsby-image].

```graphql
query {
  allShopifyProduct {
    nodes {
      id
      fields {
        featuredImage {
          fluid {
            ...ImageFragment
          }
        }
      }
    }
  }
}
```

Full example:

```graphql
{
  allShopifyProduct {
    nodes {
      id
      fields {
        featuredImage {
          fluid(maxWidth: 1000, maxHeight: 800) {
            ...GatsbyImgixFluid
          }
        }
      }
    }
  }
}
```

## Programmatic use

### For plugin developers

`gatsby-plugin-imgix` can be used to provide gatsby-image support to your
Imgix-backed plugin. Source plugins that serve Imgix URLs, such as
`gatsby-source-prismic`, use this plugin for drop-in support of GraphQL-based
image transformations.

_Docs coming soon_

[imgix]: https://www.imgix.com/
[gatsby-createnodefield]: https://www.gatsbyjs.org/docs/actions/#createNodeField
[gatsby-image]: https://www.gatsbyjs.org/packages/gatsby-image/
[prismic]: https://prismic.io/