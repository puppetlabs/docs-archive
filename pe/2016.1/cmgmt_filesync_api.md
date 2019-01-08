---
layout: default
title: "File sync API"
canonical: "/pe/latest/cmgmt_filesync_api.html"
description: "API documentation for the Puppet file sync service."
---

[file sync]: ./cmgmt_filesync.html
[Code Manager]: ./code_mgr.html

[Code Manager][] is the supported [file sync][] interface. If you don't want to use Code Manager, you can use the file sync APIs directly, although this is unsupported. The `file-sync-storage-service` and `file-sync-client-service` APIs provide several endpoints that you can use to commit code to the MoM and synchronize it with compile masters. All file sync API endpoints use SSL and port 8140 (`https://${fqdn}:8140/file-sync/v1/`).

> **Important:** This page documents the `file-sync-storage-service` and `file-sync-client-service` APIs and is intended for advanced users who want to craft their own code management workflows on PE 2015.3's file sync service. **We support and _strongly recommend_ using [Code Manager][] instead of directly accessing the file sync APIs.** File sync and Code Manager are designed to work together, and Code Manager provides a first-class interface for file sync's features. If you use Code Manager, you don't need to read any further.
>
> However, if you must access the file sync APIs directly to implement a different, unsupported code management workflow, see [About File Sync][file sync] for information about file sync's functionality, usage tips, and potential pitfalls.
>
> **Warning!** Be **very** careful if you're simply testing file sync. **By default, a file sync commit _destructively overwrites the live code directory_ on all compile masters --- including the master of masters --- with the contents of the staging directory.** Testing or experimenting with the file sync APIs can destroy your live code directory!

## Common objects

The file sync APIs use three common objects --- commit, identity, and status --- each represented as a keyed JSON map:

### Commit

A **commit** is a map of metadata that describes changes to your codebase, and uses the following keys:

* `commit`: A string that serves as a unique identifier for the commit.
* `date`: A timestamp indicating the time that the commit occurred.
* `message`: A string describing the commit. This key is **optional**; you can supply it when triggering the commit or allow file sync to generate a default value.
* `identity`: A map of information about the commit's author or initiator. See the [Identity](#identity) section.

### Identity

An **identity** is a map representing basic information about a user or system. For file sync, this typically refers to a commit's author. The file sync API represents an identity as a map with the following keys:

* `name`: A string representing the name of the user or system.
* `email`: A string representing the e-mail address of the user or system.

### Status

A file sync repository's **status** is represented in the API as a map with the following keys:

* `status`: The status of the repository after the most recent operation. Valid values are:
    * `error`: Used if there was a fatal error and the repo is in an unusable state.
    * `ok`: Used if the repository is in a normal, operational state.
    * `new-commits`: Used for client-side repositories only, and indicates that file sync fetched new commits as part of the most recent update operation. This indicates that file sync will trigger a `sync` operation to update the client's live code directory. This status changes to `ok` if the operation succeeds.
* `cause`: The cause of an error. This key is present only if the status of the repository is `error`.
* `latest_commit`: A commit object, as defined in the [Commit](#commit) section. This can be `nil` if there are no commits in the repository. This key is present only if the status of the repo is not `error`.
* `submodules`: A map containing status information for each of the repository's submodules. Each key is the submodule's path in the repo's working tree, and each key's value is itself a status map. The `submodules` key is present only if the repository is configured to use submodules --- a setting that is enabled by default --- **and** if the repository's `status` is not `error`.

#### Example status

~~~ json
{
  :puppet-code {
    :status "new-commits"
    :latest_commit {
        :commit "d295df420ee96201bda9a0faf7539ea1601c13b9"
        :date "2015-10-24T16:17:28.000Z"
        :message "Commit message"
        :identity {
          :name "Test User"
          :email "test@test.com"
        }
      }
    :submodules {
      "submodules/submodule" {
        :status "ok"
        :latest_commit {
          :commit "485003b47be1691e000447400e92bcf9838358fd"
          :date "2015-10-24T16:17:28.000Z"
          :message "Commit message"
          :identity {
            :name "Test User"
            :email "test@test.com"
          }
        }
      }
      "submodules/submodule-2" {
        :status "error"
        :cause {
          :type "puppetlabs.enterprise.file-sync-common/fetch-error"
          :message "File sync was unable to fetch from server repo.  Name: submodule-2.  Directory: /var/folders/9l/18hz0x4j5pn_fg_0tcws4xwr0000gn/T/null1442336610564-2518807265/client/repo/submodule-2.git."
          :cause #<TransportException org.eclipse.jgit.api.errors.TransportException: http://localhost:8080/file-sync-git/repo/submodule-2: Git repository not found>
        }
      }
    }
  }
}
~~~

## `file-sync-storage-service` Endpoints

### POST `/file-sync/v1/commit`

To commit code to file sync repositories, make an HTTP POST request to this endpoint.

#### Content

A `commit` request always requires a map as the request's body, with the following optional keys:

* `commit-all`: Boolean. If true and neither `repo-id` nor `submodule-id` have a value, file sync commits all repositories. If false or absent, you must provide the `repo-id` key or the commit will fail.
* `message`: The desired commit message as a string.
* `author`: The desired commit author. This should be an [Identity](#identity) map containing two keys, `name` and `email`.
* `repo-id`: The ID of the repository you want to commit. This corresponds to a key in the `repos` map of the storage service's `trapperkeeper` configuration. When provided, file sync commits only the repository with the given `repo-id`. You must provide this key if you're using the `submodule-id` key, and you must omit this key if you're using `commit-all`.
* `submodule-id`: The ID of a specific submodule to be committed within the repository specified by `repo-id`. When present, the repo specified by `repo-id` will be committed, but instead of committing all submodules, file sync commits only the submodule with the provided ID. You must also provide the `repo-id` key to use the `submodule-id` key, and you must omit this key if you're using `commit-all`.

#### Example

```
curl -s -X POST -k https://localhost:8140/file-sync/v1/commit -H "Content-type: application/json" -d '{"commit-all":true}'
```

## `file-sync-client-service` endpoints

### POST `/file-sync/v1/force-sync`

To force a one-time synchronization of all repositories at the most recently fetched commit into the live code directories, make an HTTP POST request to this endpoint.

#### Example

`curl -s -X POST -k https://localhost:8140/file-sync/v1/force-sync`

The response will be the [status](#status) of the file sync client service after the forced synchronization, excluding any service metrics.

## Authentication

File sync API endpoints use the [same authentication methods]({{puppetserver}}/config_file_auth.html) as other Puppet Server endpoints. Code Manager handles authentication for you, when manually calling file sync API endpoints on a node, you might need to provide some or all of the following depending on the node's configuration:

* the Puppet certificate authority (CA) certificate
* the target node's SSL certificate
* the target node's private key

### Example

To POST a request to the `commit` endpoint from the master of masters (MoM) using `curl`, run:

~~~
curl -X POST \
     -E <CERT>
     --key <KEY>
     --cacert <CACERT>
     -H "Content-type: application/json" \
     -d '{"commit-all":true}' \
     -k https://localhost:8140/file-sync/v1/commit
~~~

In this example, `<CERT>` represents the path to the node's SSL certificate, `<KEY>` represents the path to the node's private key, and `<CACERT>` represents the path to the Puppet certificate authority (CA) certificate.
