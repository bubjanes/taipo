<img src="icon.png" width="125" height="125" align="right" /> <img src="logo.svg" width=125 height=125 align="right">

# taipo

> taipo is a mispelling of typo, it means [evil spirit](https://en.wiktionary.org/wiki/taipo)

This app contains tools for data quality in Rasa. It can generate
augmented data but it can also check for bad labels in your training data.
The hope is this tool contributes to data that leads to more robust models.

Feedback on Non-English languages is *especially* appreciated!

## Install

You can install this experiment via pip.

```
python -m pip install "taipo @ git+https://github.com/RasaHQ/taipo.git"
```

## Usage

Taipo comes with a small suite of sub-commands.

```
> python -m taipo

  This app contains tools for data quality in Rasa. It can generate
  augmented data but it can also check for bad labels. The hope is this tool
  contributes to data that leads to more robust models.

Options:
  --help  Show this message and exit.

Commands:
  confirm   Confirms labels via a trained model.
  keyboard  Commands to simulate keyboard typos.
  translit  Commands to generate transliterations.
  util      Some utility commands.
```

### `taipo keyboard`

![](images/keyboard.png)

These tools are able to simulate keyboard typos. It uses [nlpaug](https://github.com/makcedward/nlpaug)
as a backend and supports keyboard layouts of 10 languages
(`de`, `en`, `es`, `fr`, `he`, `it`, `nl`, `pl`, `th`, `uk`). For
more details on the mapping see [here](https://github.com/makcedward/nlpaug/tree/master/nlpaug/res/char/keyboard).

```
> python -m taipo keyboard

  Commands to simulate keyboard typos.

Options:
  --help  Show this message and exit.

Commands:
  augment   Applies typos to an NLU file and saves it to disk.
  generate  Generate train/validation data with/without misspelling.
```

#### `taipo keyboard augment`

The augment command generates a single misspelled NLU file.

```
python -m taipo keyboard augment --help
Usage: keyboard augment [OPTIONS] FILE OUT

  Applies typos to an NLU file and saves it to disk.

Arguments:
  FILE  The original nlu.yml file  [required]
  OUT   Path to write misspelled file to  [required]

Options:
  --char-max INTEGER  Max number of chars to change per line  [default: 3]
  --word-max INTEGER  Max number of words to change per line  [default: 3]
  --lang TEXT         Language for keyboard layout  [default: en]
  --seed-aug INTEGER  The seed value to augment the data
  --help              Show this message and exit.
```

##### Example Usage

This example generates a new `bad-spelling-nlu.yml` file from `nlu.yml`.

```
python -m taipo keyboard augment data/nlu.yml data/bad-spelling-nlu.yml
```

This example generates does the same thing but assumes a Dutch keyboard layout.

```
python -m taipo keyboard augment data/nlu.yml data/bad-spelling-nlu.yml --lang nl
```

#### `taipo keyboard generate`

The generate command takes a single NLU file and populates your data/test folders
with relevant files to run benchmarks. Will also perform train/validation splitting.

```
> python -m taipo keyboard generate --help
Usage: keyboard generate [OPTIONS] FILE

  Generate train/validation data with/without misspelling.

  Will also generate files for the `/test` directory.

Arguments:
  FILE  The original nlu.yml file  [required]

Options:
  --seed-split INTEGER  The seed value to split the data  [default: 42]
  --seed-aug INTEGER    The seed value to augment the data
  --test-size INTEGER   Percentage of data to keep as test data  [default: 33]
  --prefix TEXT         Prefix to add to all the files  [default: misspelled]
  --char-max INTEGER    Max number of chars to change per line  [default: 3]
  --word-max INTEGER    Max number of words to change per line  [default: 3]
  --lang TEXT           Language for keyboard layout  [default: en]
  --help                Show this message and exit.
```

##### Example Usage

This command will take the original `nlu-orig.yml` file and will use it to populate
the `/test` and `/data` folders.

```
> python -m taipo keyboard generate data/nlu-orig.yml
```

The current disk state is now:

```
📂 rasa-project
┣━━ 📂 data
┃   ┣━━ 📄 nlu-train.yml                ( 667 items)
┃   ┗━━ 📄 misspelled-nlu-train.yml     ( 667 items)
┣━━ 📂 tests
┃   ┣━━ 📄 nlu-valid.yml                ( 333 items)
┃   ┗━━ 📄 misspelled-nlu-valid.yml     ( 333 items)
┗━━ 📄 nlu-orig.yml                     (1000 items)
```

### `taipo translit`

![](images/translit.png)

These tools are able to transliterate to and from a latin alphabet. It
uses [transliterate](https://github.com/barseghyanartur/transliterate) as
a backend and supports (`ru`, `mn`, `sr`, `bg`, `ka`, `uk`, `el`, `mk`, `l1`, `hy`).

```
> python -m taipo translit

  Commands to generate transliterations.

Options:
  --help  Show this message and exit.

Commands:
  augment   Applies translitertion to an NLU file and saves it to disk.
  generate  Generate train/validation data with/without translitertion.
```

#### `taipo translit augment`

Transliterates a single NLU file to and from a latin alphabet.

```
> python -m taipo translit augment --help

  Applies translitertion to an NLU file and saves it to disk.

Arguments:
  FILE  The original nlu.yml file  [required]
  OUT   Path to write misspelled file to  [required]

Options:
  --target TEXT  Alphabet to map to.  [default: latin]
  --source TEXT  Alphabet to map from.  [default: latin]
  --lang TEXT    Language for keyboard layout  [default: en]
  --help         Show this message and exit.
```

##### Example Usage

This example generates a new `greek-nlu.yml` file from `nlu.yml`.

```
python -m taipo keyboard augment data/nlu.yml data/greek-nlu.yml --target el
```

This example generates works the other way around. It assumes a Greek alphabet as
a starting point and transliterates it to the latin alphabet.

```
python -m taipo keyboard augment data/greek-nlu.yml data/latin-nlu.yml --source el
```

#### `taipo translit generate`

The generate command takes a single NLU file and populates your data/test folders
with relevant files to run benchmarks. Will also perform train/validation splitting.

```
> python -m taipo translit generate --help

  Generate train/validation data with/without translitertion.

  Will also generate files for the `/test` directory.

Arguments:
  FILE  The original nlu.yml file  [required]

Options:
  --seed INTEGER       The seed value to split the data  [default: 42]
  --test-size INTEGER  Percentage of data to keep as test data  [default: 33]
  --prefix TEXT        Prefix to add to all the files  [default: translit]
  --target TEXT        Alphabet to map to.  [default: latin]
  --source TEXT        Alphabet to map from.  [default: latin]
  --lang TEXT          Language for keyboard layout  [default: en]
  --help               Show this message and exit.
```

##### Example Usage

This command will take the original `nlu-orig.yml` file and will use it to populate
the `/test` and `/data` folders. In this case it will generate characters from the
Greek alphabet.

```
> python -m taipo translit generate data/nlu-orig.yml --prefix greek --target el
```

The following files will now be on disk.

```
📂 rasa-project
┣━━ 📂 data
┃   ┣━━ 📄 nlu-train.yml                ( 667 items)
┃   ┗━━ 📄 greek-nlu-train.yml          ( 667 items)
┣━━ 📂 tests
┃   ┣━━ 📄 nlu-valid.yml                ( 333 items)
┃   ┗━━ 📄 greek-nlu-valid.yml          ( 333 items)
┗━━ 📄 nlu-orig.yml                     (1000 items)
```


### `taipo confirm`

![](images/confirm.png)

The confirm command takes a pretrained Rasa model and runs it against one of
your nlu.yml files. The idea is that any intents that the model got wrong are
interesting candidates to double-check. There may be some confusing/incorrectly
labelled examples in your data.

```
> python -m taipo confirm --help

  Confirms labels via a trained model.

Arguments:
  NLU_PATH    The nlu.yml file to check.  [required]
  MODEL_PATH  The Rasa model to use.  [required]
  OUT_PATH    CSV output path.  [required]

Options:
  --help  Show this message and exit.
```

##### Example Usage

This command will take the `model.tar.gz` model file and run it against
the `nlu.yml` file. Any wrongly classifier examples will be saved in the
`checkthese.csv` file.

```
> python -m taipo translit nlu.yml model.tar.gz checkthese.csv
```

The `checkthese.csv` file also contains a confidence level, indicating
the confidence that the model had while making the prediction. When a model
shows high confidence on a wrong label, it deserves priority.

### `taipo util`

We host some utility methods to transform intent-based data from .csv to .yml.
Be aware, these methods ignore entities!

```
> python -m taipo util

  Some utility commands.

Options:
  --help  Show this message and exit.

Commands:
  csv-to-yml  Turns a .csv file into nlu.yml for Rasa
  yml-to-csv  Turns a nlu.yml file into .csv
```

#### `taipo util csv-to-yml`

```
> python -m taipo util csv-to-yml --help
Usage: util csv-to-yml [OPTIONS] FILE

  Turns a .csv file into nlu.yml for Rasa

Arguments:
  FILE  The csv file to convert  [required]

Options:
  --out PATH        The path of the output file.  [default: .]
  --text-col TEXT   Name of the text column.  [default: text]
  --label-col TEXT  Name of the label column.  [default: intent]
  --help            Show this message and exit.
```

#### `taipo util yml-to-csv`

```
> python -m taipo util csv-to-yml --help
Usage: __main__.py util yml-to-csv [OPTIONS] FILE

  Turns a nlu.yml file into .csv

Arguments:
  FILE  The csv file to convert  [required]

Options:
  --out PATH  The path of the output file.  [default: .]
  --help      Show this message and exit.
```

## Roadmap

- Implement phonetic typo generator. Not all spelling mistakes are caused by a keyboard after all.
