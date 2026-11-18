# Multi-Station Seismic Waveform Prediction with ConvLSTM3D and LSTM

This repository provides the training and inference code for a multi-station seismic waveform prediction model. The model predicts the EW and NS waveform components at target stations from seismic records at input stations.

## Dataset

The dataset used in this study is publicly available at: https://doi.org/10.6084/m9.figshare.34981531

## Installation

### Requirements

- Python 3.9 or later
- PyTorch
- NumPy
- Matplotlib
- tqdm
- joblib
- CUDA

## Usage

### Training Configuration

The main configuration options in `Training model code.py` are listed below:

| Option | Default value | Description |
| --- | --- | --- |
| train_data_dir | autodl-tmp/train/111 | Training dataset path |
| val_data_dir | autodl-tmp/val/1 | Validation dataset path |
| input_station_filter | ['ALP', 'MOP', 'PLS', 'RIO'] | Input station filter |
| output_station_filter | None | Use all available output stations |
| input_component_dirs | ['x', 'y'] | Input component directories |
| output_component_dirs | ['px', 'py'] | Target component directories |
| time_steps | 25000 | Maximum number of time points read from each waveform |
| total_epochs | 80 | Maximum number of epochs per training run |
| batch_size | 64 | Training batch size |
| num_runs | 1 | Number of independent training runs with random initialization |

### Model Training

Main training parameters:

- Optimizer: AdamW
- Learning rate: 0.001
- Weight decay: 0.00001
- Batch size: 64
- Gradient accumulation steps: 2
- Training epochs: 80
- Early-stopping patience: 30

First, set the training and validation dataset paths in `CONFIG`:

```python
'train_data_dir': "autodl-tmp/train/111",
'val_data_dir': "autodl-tmp/val/1",
```

Ensure that `Training model code.py` contains the following entry point at the end of the file:

```python
if __name__ == "__main__":
    main_pipeline()
```

Then run:

```bash
python "Training model code.py"
```

### Training Outputs

Each training execution creates a timestamped directory:

```text
baseline_single_stage_YYYYMMDD_HHMMSS/
└── baseline_run_1.pth
```

## Model Testing and Inference

### Preparing Trained Weights

Place the `.pth` files to be evaluated in a directory, for example:

```text
selected_model/
└── baseline_run_1.pth
```

### Inference Configuration

Open `Using the model code.py` and set the following paths in the `main()` function:

```python
models_dir = r"selected_model"
output_root_dir = r"reviewer_inference_results"
test_data_dir = r"test_set"
```

Also verify the following data settings:

```python
INPUT_STATION_FILTER = ['ALP', 'MOP', 'PLS', 'RIO']
OUTPUT_STATION_FILTER = None
```

`SAMPLING_RATE` determines the time column in the exported CSV files. The default sampling rate is 100 Hz, corresponding to a time interval of `dt = 0.01 s`.

### Running Inference

Run:

```bash
python "Using the model code.py"
```