# Parallel vs Serial Calculator

A Jupyter notebook that benchmarks element-wise arithmetic (add, subtract, multiply, divide) on 1,000,000 numbers. It compares:

- **Serial vs parallel** execution (using `multiprocessing.Pool`)
- **Unoptimized vs optimized** code (plain Python loops vs NumPy)

## Results

Tested with N = 1,000,000 and 8 worker processes:

| Approach | Time (s) |
|---|---|
| Serial, unoptimized (Python loops) | 0.7953 |
| Serial, optimized (NumPy) | 0.0206 |
| Parallel, unoptimized (Python loops) | 6.6065 |
| Parallel, optimized (NumPy) | 0.2174 |

## Key Takeaways

- **NumPy is much faster than plain Python loops.** In serial, the optimized version was about 38x faster than the unoptimized one.
- **Parallel was slower than serial in this test.** Each operation is very cheap, so the cost of sending data to the worker processes and collecting results (pickling and inter-process communication) was larger than the time saved by splitting the work.
- **Parallelism pays off when each task does a lot of work relative to the data it needs.** Simple element-wise math on large arrays is a poor fit; heavier computations per item would likely show a real speedup.

## How It Works

1. The notebook writes the worker functions (`slow` and `fast`) to a helper file, `calc_mod.py`, so `multiprocessing` works inside Jupyter.
2. It generates two random arrays of 1,000,000 numbers each (the second offset by +1 to avoid division by zero).
3. **Serial:** runs all four operations one after another.
4. **Parallel:** splits the arrays into one chunk per CPU core and processes the chunks with a process pool.
5. A warm-up call starts the pool before timing, so startup cost is not included in the results.

## Requirements

- Python 3.8 or newer
- NumPy
- Jupyter Notebook or JupyterLab

```bash
pip install numpy notebook
```

## Running the Notebook

```bash
git clone https://github.com/emanified/YOUR-REPO-NAME.git
cd YOUR-REPO-NAME
jupyter notebook calculator.ipynb
```

Then choose **Kernel > Restart & Run All**. Your timings will differ depending on your CPU and core count.

## Files

| File | Description |
|---|---|
| `calculator.ipynb` | The benchmark notebook |
| `calc_mod.py` | Generated automatically when the notebook runs |
