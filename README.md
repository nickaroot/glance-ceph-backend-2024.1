# How to use Ceph as a OpenStack Glance backend

#### Setup Ceph Client Authentication
- Create Glance keyrings
  ```
  ceph auth get-or-create client.glance mon 'profile rbd' osd 'profile rbd pool=images' mgr 'profile rbd pool=images' > /etc/ceph/ceph.client.glance.keyring
  ```

- Duplicate ceph.conf to ceph-glance.conf with impicit keyring
  ```ini
  [global]
    ...
    keyring = /etc/ceph/ceph.client.glance.keyring
  ```

#### Edit /etc/glance/glance-api.conf
  - Add `rdb` storage to `enabled_backends` in `[DEFAULT]` section. You can pick any name instead of `ceph`
    
    ```
    enabled_backends = ceph:rbd
    ```
  - Set `default_backend` to `ceph` in `[glance_store]` section
    
    ```
    default_backend = ceph
    ```
  - Create new `ceph` section
    
    ```
    [ceph]
    store_description = "Ceph"
    rbd_store_chunk_size = 8
    rbd_store_pool = images
    rbd_store_user = glance
    rbd_store_ceph_conf = /etc/ceph/ceph-glance.conf
    ```

#### Restart Glance
```
sudo glance-control api restart
```
