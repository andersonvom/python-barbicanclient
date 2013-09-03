# python-barbicanclient

This is a client for the [Barbican](https://github.com/stackforge/barbican)
Key Management API.  There is a Python library for accessing the API
(`barbicanclient` module), and a command-line script (`keep`).

## barbicanclient - Python API
The full api is documented in the wiki.

### Quickstart
Store a secret in barbican using keystone for authentication:
```python
>>> from barbicanclient.common import auth
>>> from barbicanclient import client
# We'll use keystone for authentication
>>> keystone = auth.KeystoneAuth(auth_url='http://keystone-int.cloudkeep.io:5000/v2.0', 
...                              username=USER, password=PASSWORD, tenant_name=TENANT)
>>> barbican = client.Client(auth_plugin=keystone)
# Let's store some sensitive data, Barbican encrypts it and stores it securely in the cloud
>>> secret_id = barbican.secrets.store(name='Self destruction sequence', 
...                                    payload='the magic words are squeamish ossifrage', 
...                                    payload_content_type='text/plain')
# Let's look at some properties of a barbican Secret
>>> secret = barbican.secrets.get(secret_id)
>>> print(secret.id)  
d46883b4-e072-4452-98c9-36d652dfcdd6
>>> print(secret.name)
Self destruction sequence
# Now let's retrieve the secret payload.  Barbican decrypts it and sends it back.
>>> print(barbican.secrets.raw(secret.id, secret.content_types['default']))
the magic words are squeamish ossifrage
```

## keep - Command Line Client


```
$ keep -h
usage: keep [-h] [--no-auth | --os-auth-url <auth-url>]
            [--os-username <auth-user-name>] [--os-password <auth-password>]
            [--os-tenant-name <auth-tenant-name>] [--os-tenant-id <tenant-id>]
            [--endpoint <barbican-url>]
            {order,secret} {create,store,get,list,delete} ...

Command-line interface to the Barbican API.

positional arguments:
  {order,secret}        Entity used for command.

optional arguments:
  -h, --help            show this help message and exit
  --no-auth, -N         Do not use authentication
  --os-auth-url <auth-url>, -A <auth-url>
                        Defaults to env[OS_AUTH_URL].
  --os-username <auth-user-name>, -U <auth-user-name>
                        Defaults to env[OS_USERNAME].
  --os-password <auth-password>, -P <auth-password>
                        Defaults to env[OS_PASSWORD].
  --os-tenant-name <auth-tenant-name>, -T <auth-tenant-name>
                        Defaults to env[OS_TENANT_NAME].
  --os-tenant-id <tenant-id>, -I <tenant-id>
                        Defaults to env[OS_TENANT_ID].
  --endpoint <barbican-url>, -E <barbican-url>
                        Defaults to env[BARBICAN_ENDPOINT].

subcommands:
  Action to perform

  {create,store,get,list,delete}
    create              Create a new order.
    store               Store a secret in barbican.
    get                 Retrieve a secret or an order by providing its UUID.
    list                List secrets or orders
    delete              Delete a secret or an order by providing its UUID.

```
