# ICE: Item Concept Embedding via Textual Information

## About
The ICE toolkit implements the model described in ICE: Item Concept Embedding via Textual Information (SIGIR 2017). 

ICE is designed to embed concepts into items such that items can be compared in terms of their overall conceptual similarity regardless of entity types. For example, a song can be used to retrieve conceptually similar songs (homogeneous) as well as concepts or movies (heterogeneous). 

Specifically, there are two stages in the ICE framework. In the graph construction stage, ICE incorporates items and their representative concepts (words extracted from the textual information) using a heterogeneous network. Then, in the embedding learning stage, ICE learns the embeddings for items and concepts using concepts as the context shared between heterogeneous entities.

Since items are defined in terms of concepts, adding expanded concepts into the network allows the embeddings to be used to retrieve conceptually more diverse and yet relevant results.


## Developement Environment:
- g++ > 4.9
- python3
- cython

## Getting Started
c++ compilation
```
$ git clone https://github.com/CLIPLab/ICE
$ cd ./ICE/ICE
$ make cli
$ ./cli
```
python3 API compilation
```
make python
```

## Construct ICE graph
```
$ python graph.py -i ./data/lyrics_small.csv -w ./data/word-edges_en.txt -o network.edgelist
```
Parameters:
```
  -i <string>, --input <string>
          Input File Name
  -w <string>, --wordedges <string>
          Word Edges File Name
  -o <string>, --output <string>
          Output File Name
```
Sample files: See `data/lyrics_small.csv` and `data/word-edges_en.txt`
## Example Input
network.edgelist
```txt
五月天 Taiwanese 1
五月天 rock 1
五月天 band 1
MAYDAY@ Taiwanese 1
MAYDAY@ rock 1
MAYDAY@ band 1
Sodagreen Taiwanese 1
Sodagreen indie 1
Sodagreen pop_rock 1
Sodagreen band 1
SEKAI_NO_OWARI Japanese 1
SEKAI_NO_OWARI indie 1
SEKAI_NO_OWARI pop_rock 1
SEKAI_NO_OWARI band 1
The_Beatles England 1
The_Beatles rock 1
The_Beatles pop 1
```

## Command Line Interface

Parameters:
```
Options:
        -train <string>
                Train the Network data
        -save <string>
                Save the representation data
        -dimensions <int>
                Dimension of vertex representation; default is 64
        -negative_samples <int>
                Number of negative examples; default is 5
        -sample_times <int>
                Number of training samples *Million; default is 10
        -threads <int>
                Number of training threads; default is 1
        -alpha <float>
                Init learning rate; default is 0.025
```

Example Usage:
```
./cli -train network.edgelist -save rep.txt -dimensions 4 -sample_times 10 -negative_samples 5 -alpha 0.025 -threads 1
```

## Python3 API usage
After the compilation of python3, see example.py for example usage
```python
from pyICE import pyICE

ice = pyICE()
network = {
    '五月天': {
        'Taiwanese': 1,
        'rock': 1,
        'band': 1
        },
    'MAYDAY@': {
        'Taiwanese': 1,
        'rock': 1,
        'band': 1
        },
    'Sodagreen': {
        'Taiwanese': 1,
        'indie': 1,
        'pop_rock': 1,
        'band': 1
        },
    'SEKAI_NO_OWARI': {
        'Japanese': 1,
        'indie': 1,
        'pop_rock': 1,
        'band': 1
        },
    'The_Beatles': {
        'England': 1,
        'rock': 1,
        'pop': 1
        }
}
ice.load_dict(network)
ice.init(dimension=4)
ice.train(sample_times=11, negative_samples=5, alpha=0.025, workers=1)
ice.save_weights(model_name='ICE.rep')
```

## Example Output (the Learned Representations)
```txt
五月天 0.47944 1.03708 -1.78878 -0.856039
MAYDAY@ 0.48029 1.04692 -1.78591 -0.855289
Sodagreen -0.849743 0.435535 -1.41524 1.75017
SEKAI_NO_OWARI 1.23796 -1.43277 -1.12657 1.28416
The_Beatles 2.21267 1.66775 0.42715 0.702507
```
