# Deflect Core

This project serves as the core of Deflect, several componenet is integrated within this project including edgemanage, Deflect website DNS/options, gen_site_config and autodeflect.

## Versions

1. Python 3.6.10
2. Django 3.1
3. Django REST framework 3.11.1

## Install (dev)

```
python -m venv venv
source venv/bin/activate

# Init submodule edgemanage before pip
git submodule update --init
cd edgemanage3
python setup.py install
cd ..
pip install -r requirements.txt

# Create super user for django
python manage.py createsuperuser --email admin@example.com --username admin
```

## Post-install

After executing `python setup.py install` for edgemanage, there will be 3 binary installed

- edge_manage
- edge_query
- edge_conf

Directly executing these command should work as usual, but **an edgemanage config yaml is required** before running such command

1. `cp dev/edgemanage/edgemanage.example.yaml dev/edgemanage/edgemanage.yaml`
2. Edit `edgemanage.yaml`, replace `<abs_path>` with absolute path of this project directory (without trailing `/`)
3. Create `dev/edgemanage/edges/dev` and insert edges hostname, line by line

Execute commands to ensure edgemanage is installed correctly

- `edge_manage --dnet dev --config dev/edgemanage/edgemanage.yaml -v`
- `edge_conf --dnet dev --config dev/edgemanage/edgemanage.yaml --mode unavailable --comment "out" {edge_hostname}`
- `edge_query --dnet dev --config dev/edgemanage/edgemanage.yaml -v`

## Django Admin

Built-in admin interface can be accessed via [http://localhost:8000/admin](http://localhost:8000/admin)

## REST framework GUI

REST framework provides built-in GUI for API testing, auth is required by clicking "Log in" on the top right corner when accessed via [http://localhost:8000/api](http://localhost:8000/api)

## API Authentication

API token should be provided via header

    Authorization: Bearer 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b

Token could be generated by:

1. Django admin panel
2. `python manage.py drf_create_token <username>`
3. POST `http://localhost:8000/api-token-auth/` with field `username` and `password`

## API Implementation

### GET `/api/edge/list`

#### Param

- dnet

Equivalent to command `edge_query --dnet dnet1`

#### Sample output

```
[
    {
        "edgename": "one22.prod.deflect.ca",
        "mode": "available",
        "state": "out",
        "health": "pass",
        "state_time": -1,
        "comment": null
    }
]
```

### POST `/api/edge/dnet`

List all available dnet

#### Sample output

```
[
    'dnet1',
    'dnet2',
    'dnet3'
]
```


### POST `/api/edge/config`

#### Param

1. dnet
2. edge: edge hostname
3. mode: available / unavailable
4. comment
5. comment_user

#### Sample output

```
{
    "edge": "lime20.prod.deflect.ca",
    "prev_state": {
        "mode": "available",
        "comment": "[jeremy_pm] hi hi"
    },
    "current_state": {
        "mode": "available",
        "comment": "[jeremy_pm] hi hi"
    }
}
```

## Tests

    python test.py