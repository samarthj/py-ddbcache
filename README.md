# ddbcache

A simple interface for caching items in DynamoDB

[![Release](https://github.com/samarthj/py-ddbcache/actions/workflows/release.yml/badge.svg)](https://github.com/samarthj/py-ddbcache/actions/workflows/release.yml)
[![GitHub release (latest SemVer including pre-releases)](https://img.shields.io/github/v/release/samarthj/py-ddbcache?sort=semver)](https://github.com/samarthj/py-ddbcache/releases)
[![PyPI](https://img.shields.io/pypi/v/ddb-cache)](https://pypi.org/project/ddb-cache/)

[![Build](https://github.com/samarthj/py-ddbcache/actions/workflows/build_matrix.yml/badge.svg)](https://github.com/samarthj/py-ddbcache/actions/workflows/build_matrix.yml)
[![codecov](https://codecov.io/gh/samarthj/py-ddbcache/branch/main/graph/badge.svg?token=9VCCD1BDNY)](https://codecov.io/gh/samarthj/py-ddbcache)

[![Total alerts](https://img.shields.io/lgtm/alerts/g/samarthj/py-ddbcache.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/samarthj/py-ddbcache/alerts/)
[![Language grade: Python](https://img.shields.io/lgtm/grade/python/g/samarthj/py-ddbcache.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/samarthj/py-ddbcache/context:python)

[![pdm-managed](https://img.shields.io/badge/pdm-managed-blueviolet)](https://pdm.fming.dev)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)

## Example Usage

Samples can be found here in the [tests](https://github.com/samarthj/py-ddbcache/blob/main/tests/test_db.py)

The library can also be used for crud operations since it implement `get`, `put`, `update`, `delete`, `scan` and `query`. Along with `create_backup`.

Basic usage is as given below:

```python
def test_put_cache(ddb_cache: DDBCache):
    data = randint(1, 100)  # noqa: S311
    ddb_cache.put_cache({"id": "test_put_cache", "data": data})
    item = ddb_cache.get_item({"id": "test_put_cache"})
    assert item
    assert item["id"] == "test_put_cache"
    assert item["data"] == data
    assert item["ttl"]


def test_put_cache_no_ttl(ddb_cache: DDBCache):
    data = randint(1, 100)  # noqa: S311
    ddb_cache.put_cache({"id": "test_put_cache", "data": data}, with_ttl=False)
    item = ddb_cache.get_item({"id": "test_put_cache"})
    assert item
    assert item["id"] == "test_put_cache"
    assert item["data"] == data
    with pytest.raises(KeyError):
        item["ttl"]


def test_fetch_cache(ddb_cache: DDBCache, init_cache):
    item = ddb_cache.get_item({"id": "test_fetch_cache"})
    assert item
    assert item["id"] == "test_fetch_cache"
    assert item["data"] == "test_fetch_cache"
    ttl = item["ttl"]
    assert ttl
    sleep(1)
    item = ddb_cache.fetch_cache({"id": "test_fetch_cache"})
    assert item
    assert item["id"] == "test_fetch_cache"
    assert item["data"] == "test_fetch_cache"
    with pytest.raises(KeyError):
        item["ttl"]
    item = ddb_cache.get_item({"id": "test_fetch_cache"})
    assert item
    assert item["id"] == "test_fetch_cache"
    assert item["data"] == "test_fetch_cache"
    ttl2 = item["ttl"]
    assert ttl2
    assert ttl != ttl2


def test_fetch_cache_no_ttl_update(ddb_cache: DDBCache, init_cache):
    item = ddb_cache.get_item({"id": "test_fetch_cache"})
    assert item
    assert item["id"] == "test_fetch_cache"
    assert item["data"] == "test_fetch_cache"
    ttl = item["ttl"]
    assert ttl
    item = ddb_cache.fetch_cache({"id": "test_fetch_cache"}, with_ttl=False)
    assert item
    assert item["id"] == "test_fetch_cache"
    assert item["data"] == "test_fetch_cache"
    with pytest.raises(KeyError):
        item["ttl"]
    item = ddb_cache.get_item({"id": "test_fetch_cache"})
    assert item
    assert item["id"] == "test_fetch_cache"
    assert item["data"] == "test_fetch_cache"
    ttl2 = item["ttl"]
    assert ttl2
    assert ttl == ttl2
```
