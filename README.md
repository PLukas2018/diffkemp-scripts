# Scripts for running DiffKemp with EqBench dataset

- Contains scripts for running DiffKemp with EqBench dataset, for summarisation
  and analysis of the results.
- Dataset was introduced in this paper:
  Badihi S, Li Y, Rubin J. "EqBench: A Dataset of Equivalent and Non-equivalent
  Program Pairs", IEEE/ACM 18th International Conference on Mining Software
  Repositories (MSR), data showcase, pp. 610-614, 2021.
- Dataset is available from <https://github.com/shrBadihi/EqBench>.


## Requirements

To use the scripts you need to install python requirements:

```bash
pip install -r requirements.txt
```

## Usage

Firstly, you need to clone this repo to diffkemp repository.
Then you can run the EqBench analysis like this (takes approximately 15 min):

```bash
EqBench-workflow/analysis compare-multiple output_dir path_to_eqbench_repo > results.csv
```

where

- `output_dir` is a directory where you want to save the results,
- `path_to_eqbench_repo` is a path to the directory containing the cloned
  [EqBench repo](https://github.com/shrBadihi/EqBench) (it expects the parent
  directory, not the named `benchmarks`)
- `results.csv` file for saving the summary of the results.

Results are saved to `results.csv`, the file can look something like this:

```csv
Type;opt level;using custom llvm passes;patterns;eq/eq;eq/neq;neq/neq;neq/eq
changes only in one function;-O2;opt level ones;default;91;41;111;0
changes in all functions;-O2;opt level ones;default;7;5;12;0
changes only in one function;-O1;custom (build-kernel ones);default;54;78;111;0
changes in all functions;-O1;custom (build-kernel ones);default;2;10;12;0
```

The most important results are the one that begins with
`changes only in one function`.

Meaning of fields:

- `Type`
  - `changes only in one function`: results for programs that contain
    differences only in one function,
  - `changes in all functions`: results for programs which aggregate differences
    from previously mentioned programs
    (contains differences in multiple functions) -- these results are less
    interesting than results for the `changes only in one function`,
- `opt level`: optimization level (O1, O2, ...)
- `using custom llvm passes`:
  - `opt level ones`: uses LLVM passes that belong to the optimization level
    (O1, O2, ...)
  - `custom (build-kernel ones)`: uses LLVM passes which DiffKemp uses when
    building snapshots of kernels,
- `patterns`:
  - `default`: DiffKemp default built-in patterns were used,
  - `all-disabled`: all DiffKemp built-in patterns were disabled,
- meaning of `eq/eq,...` is `expected result/result of diffkemp compare`.

By default, the analysis is run for:

- O2 optimization with O2 passes and default patterns,
- O1 optimization with O1 passes and default patterns,
- O1 optimization with custom passes and default patterns.

These are probably the most interesting options, but you can add more in `RUN`
variable in `analysis` or you can run `EqBench-workflow/run` directly which
provides more options.

More detailed info about the results of the benchmark is located in
`output_dir`:

- `output_dir/run/result.md` contains the summary of the results,
- `output_dir/run/eqbench-results.csv` contains info about the result of every
  program,

the `run` in the path is meant to be:

- `kernel-opt-O1-default-patterns` for O1 optimization with custom passes and
  default patterns,
- `default-opt-O1-default-patterns` for O1 optimization with O1 passes and
  default patterns.
- `default-opt-O2-default-patterns` for O2 optimization with O2 passes and
  default patterns.
