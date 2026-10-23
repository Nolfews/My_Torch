# My_Torch

My_Torch is a Python project for analyzing chess positions. It contains:

- a parser for positions in FEN format;
- an encoder that transforms a FEN position into a numerical vector that can be used by a neural network;
- a multilayer perceptron implemented without a deep learning framework;
- an analyzer that can classify a position as `Nothing`, `Check`, or `Checkmate`;
- unit tests for the FEN parser and encoder.

## Installation

The project requires Python 3.10 or later. From the project root:

```bash
python -m venv .venv
```

Then activate the virtual environment:

```bash
# Windows PowerShell
.venv\Scripts\Activate.ps1

# Linux / macOS
source .venv/bin/activate
```

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```

## Using the FEN Parser

The parser accepts a complete FEN or a FEN containing only the first four required
fields. The encoder produces a NumPy vector with 781 values:
768 values for the pieces, 1 for the active player, 4 for castling rights, and
8 for the en passant file.

```python
from fen_parser.fen_parser import FENParser
from fen_parser.fen_encoder import FENEncoder

fen = "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1"

position = FENParser().parse(fen)
print(position)

vector = FENEncoder().encode(position)
print(vector.shape)  # (781,)
```

To validate a FEN without displaying an exception:

```python
from fen_parser.fen_parser import FENParser

if FENParser.is_valid_fen(fen):
	print("Valid FEN")
```

## Using the Neural Analyzer

The `my_torch_network.nn` file is the model provided with the project. The analyzer
reads a text file containing one FEN position per line.

In prediction mode, each line contains a complete FEN without a label:

```text
rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1
```

Run a prediction with:

```bash
python my_torch_analyzer --predict my_torch_network.nn positions.fen
```

In training mode, add the expected label at the end of each line:

```text
rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1 Nothing
```

The accepted labels are `Nothing`, `Check`, and `Checkmate`. To train the
model and save the result to a new file:

```bash
python my_torch_analyzer --train --save my_torch_network_trained.nn my_torch_network.nn training.fen
```

View all available options with:

```bash
python my_torch_analyzer --help
```

## Tests

From the project root:

```bash
python -m pytest tests/ -v
```

To generate a coverage report:

```bash
python -m pytest tests/ --cov=fen_parser --cov-report=term-missing --cov-report=html
```

The corresponding scripts are located in `scripts/` and can be used from a
Bash-compatible shell.
