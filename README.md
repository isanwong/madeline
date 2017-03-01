# Madeline Pedigree Drawing Engine Web Service

Web service to generate pedigree diagrams utilizing the
Madeline 2.0 Pedigree Drawing Engine (http://madeline.med.umich.edu/madeline).

## API

---

### Version

Returns version (Github Commit SHA and Date) of compiled madeline PDE binary.

The madeline PDE binary is compiled from the Madeline PDE source code
located at https://github.com/piratical/Madeline_2.0_PDE.

#### URL

`http://<app>/version`

#### Methods

##### GET

Parameters:

    None

Response:

    <git_url> - <datestamp_of_commit>

Example:

    $ curl http://<app>/version

    https://github.com/piratical/Madeline_2.0_PDE/tree/8ab82ad - 2016-12-21 00:38:52 -0500

---

### Submit

Submit data and command line arguments to render a pedigree diagram

#### URL

    http://<app>/submit

#### Methods

##### POST

Parameters:

- `data` - The text or XML for madeline to use as input to render the diagram

- `args` - List of command line arguments to pass to the madeline PDE binary

Response (Success):

    {
      'status': 'success',
      'svg': <svg_data>,
      'command': <command>,
      'command_output': <command_output>
    }

Response (Error):

    {
      'status': 'error',
      'reason': <reason_code>,
      'command': <command>,
      'command_output': <command_output>
    }

Example:

*Note: Test data available at:* http://madeline.med.umich.edu/madeline/testdata/

    $ cat madeline/data/cs_001.data

    Individualid	Familyid	Gender	Mother	Father
    m100	cs_001	m	.	.
    m101	cs_001	f	.	.
    m102	cs_001	m	m101	m100
    m103	cs_001	f	m101	m100
    m104	cs_001	m	.	.
    m105	cs_001	f	m101	m100
    m106	cs_001	m	.	.
    m107	cs_001	m	m101	m100
    m109	cs_001	m	m103	m104
    m108	cs_001	m	m103	m104
    m110	cs_001	m	m114	m108
    m111	cs_001	m	m114	m108
    m114	cs_001	f	m105	m106
    m112	cs_001	m	m105	m106


    $ json_escape() { printf '%s' $1 | python -c 'import json,sys; print(json.dumps(sys.stdin.read()))'; }

    $ echo "'args': [ '--color', '--noiconlabels' ], 'data': '$(json_escape $(cat data/cs_001.data))' }" > tmp/request.json

    $ curl -X POST -H 'Content-Type:application/json' \
      --data args='["--color", "noiconlabels"]' \
      --data-urlencode data@madeline/data/cs_001.data http://localhost:5000/submit

    $ curl -X POST \
      --data-urlencode data@madeline/data/cs_001.data http://localhost:5000/submit



