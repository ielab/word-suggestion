# Word Suggestion Module for Search Refiner

Give a word, provide its alternatives based on PMI score (Elastic Search Index) and Cosine Similarity (CUI2Vec)

Wrapped in a waitress server as a RESTful API to provided alternative terms and their scores

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

What things you need to install the software and how to install them

```
pip3 install waitress flask flask-restful flask_jsonpify requests nltk
```

If you are trying to deploy it locally, install `flask-cors`
```
pip3 install flask-cors
```

### Development

To run in development mode

Make sure to add a `config.json` file to configure the server and the elastic search index

After setting up the config file, use the following command to run the development server:

```
python3 main.py --dev
```
or
```
python3 main.py -d
```
or
```
python3 main.py
```

The service will be deployed to host and port specified in config

The API can be accessed through:

```
http://localhost:6688/search?retSize=10&pool=20&sources=cui,es&merged=false&term=cancer
```

Parameters:
```
retSize = 10
pool = 20
sources = cui,es
merged = false
term = cancer
```

Where `retSize` and `pool` are optional params, `retSize` specifies the how many alternative terms to be returned from the search, `pool` specifies the number of relevant documents used in elastic search index for finding the alternative terms. (`retSize` default is 10 and the `pool` is 20).

Added another two parameters `sources` and `merged`, where `sources` allow users to specify which source of the words suggestions come from, can be multiple sources, currently supporting `cui` (CUI2Vec) and `es` (elastic search index) separated by comma `,`, `merged` allow users to choose if the results are merged together or separated from each other, if merged, the scores will be normalized, if separated, the scores are their original scores.

### Production

For production server, configure the host for waitress server in `config.json` file, a template is provided as `config.json.template`
The `ES` specifies the Elastic Search Index which contains all the PubMed Articles
The `Cui2Vec` is just a simple binary file which is pre-generated that contains the similarity scores of each pair of CUI
And using waitress server by calling

```
python3 main.py --prod
```
or
```
python3 main.py -p
```

By calling API:

```
http://localhost:6688/search?retSize=5&pool=20&sources=cui,es&merged=false&term=cancer
```

The result is in `json` format:

```
{
    "CUI": [
        {
            "cui": 521158,
            "score": 0.226,
            "term": "neoplasm recurrence"
        },
        {
            "cui": 2939419,
            "score": 0.225,
            "term": "secondary malignancies"
        },
        {
            "cui": 445092,
            "score": 0.225,
            "term": "no metastases (tumor staging)"
        },
        {
            "cui": 741899,
            "score": 0.221,
            "term": "poorly differentiated carcinoma"
        },
        {
            "cui": 334277,
            "score": 0.221,
            "term": "metastatic adenocarcinoma"
        }
    ],
    "ES": [
        {
            "score": 0.25,
            "term": "surveys"
        },
        {
            "score": 0.134,
            "term": "medical"
        },
        {
            "score": 0.13,
            "term": "demand"
        },
        {
            "score": 0.118,
            "term": "reeducation"
        },
        {
            "score": 0.115,
            "term": "1958"
        }
    ]
}

```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
