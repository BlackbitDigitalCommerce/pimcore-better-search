# Pimcore Better Search

For efficient data maintenance it is mandatory to have an efficient search. This bundle replaces Pimcore's backend search functionality to offer a search experience like users know from popular web search engines.

It does not need any configuration or external applications to be installed.

## Problems with Pimcore's built-in search functionality

Pimcore provides an object search in

- global search
- relation field search (via looking glass icon)
- grid search

All of these places share the same underlying search engine and thus the same problems:

- by default (without MySQL's search operators) only word search is supported
  - e.g. searching `product` will not find a data object which only contains `products`
- terms with less than 3 characters get ignored (with MySQL's default `innodb_ft_min_token_size` setting)
  - e.g. searching for `CD` will never find anything
- there is no fuzzy search which searches tolerating typo errors
  - e.g. searching for `produt` will not find `product`
- there is no word-stemming
  - e.g. searching for `shirts` (plural) will not find an object which contains `shirt`
- sometimes order of search results is irritating
  - e.g. elements which contain the search term only once get listed higher than those which contain it multiple times
- search is unusable for languages which do not have word separators (like Chinese, Japanese)

## What does the plugin do different?

This bundle uses [TNT Search](https://github.com/teamtnt/tntsearch) as underlying library. This library solves all above issues, as it:

- supports prefix and suffix search
- supports any word length
- implements fuzzy / typo-tolerant search
- implements word-stemming for many languages
- implements [TF/IDF algorithm](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) to better sort search results
- implements n-gram search
  - for use with languages like Chinese / Japanese which do not have word separators

## Installation

### Composer

To get the plugin code you have to buy the plugin or write an email to [info@blackbit.de](mailto:info@blackbit.de).

You then either get access to the bundle's [Bitbucket repository](https://bitbucket.org/blackbitwerbung/pimcore-plugins-better-search) or you get the plugin code as a zip file. Accessing the Bitbucket repository has the advantage that you will always see changes to the plugin in the pull requests and are able to update to a new version yourself - please visit [this page](https://shop.blackbit.de/de/service-xt-commerce/bitbucket-zugriff-xt-commerce-plugin-entwicklung) if this sounds interesting to you - if it does, please send us the email address of your BitBucket account so we can allow access to the repository.

When we allow your account to access our repository, please add the repository to the `composer.json` in your Pimcore root folder (see [Composer repositories](https://getcomposer.org/doc/05-repositories.md#vcs)):

```json
"repositories": [
    {
        "type": "vcs",
        "url": "git@bitbucket.org:blackbitwerbung/pimcore-plugins-better-search"
    }
]
```

(Please [add your public SSH key to your Bitbucket account](https://support.atlassian.com/bitbucket-cloud/docs/add-access-keys/#Step-3.-Add-the-public-key-to-your-repository) for this to work)

Alternatively if you received the plugin code as zip file, please upload the zip file to your server - e.g. create a folder `bundles` in the Pimcore root folder) and add the following to your `composer.json`:

```json
"repositories": [
    {
        "type": "artifact",
        "url": "./bundles/"
    }
]
```

Beware that when you put the zip directly in the Pimcore root folder, and add `"url": "./"` it will still work but Composer will scan *all* files under the Pimcore root recursively to find bundle zip files (incl. assets, versions etc) - which will take quite a long time.

Then you should be able to execute `composer require blackbit/better-search` (or `composer update blackbit/better-search --with-dependencies` for updates if you already have this bundle installed) from CLI.

At last you have to enable and install the plugin, either via browser UI or via CLI `bin/console pimcore:bundle:enable BlackbitBetterSearchBundle && bin/console pimcore:bundle:install BlackbitBetterSearchBundle`

You can always access the latest version by executing `composer update blackbit/better-search --with-dependencies` on CLI.
