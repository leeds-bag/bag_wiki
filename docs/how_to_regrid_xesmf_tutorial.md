# Tutorial: Regridding 2D NetCDF Datasets in Python with xESMF

Author: Callum 

Regridding (also called remapping or resampling) is a common task in geosciences, especially when working with gridded data such as satellite or climate model outputs. The goal is to interpolate data from one grid to another, which is essential for comparing datasets, combining products, or preparing data for models.

In this tutorial, we'll use the Python package **xESMF** to regrid 2D NetCDF datasets. xESMF is built on top of xarray and ESMF, providing a simple interface for regridding with various algorithms.

## Prerequisites

Install the required packages:

```bash
mamba install xarray xesmf
```

## 1. Loading a NetCDF Dataset

We'll use xarray to open NetCDF files. Here, we assume you have a 2D variable (e.g., satellite data) with latitude and longitude coordinates.

```python
import xarray as xr

ds = xr.open_dataset('input_data.nc')
print(ds)
```

### Plotting the Input Data

```python
import matplotlib.pyplot as plt

ds['your_variable'].plot()
plt.title('Original Data on Source Grid')
plt.show()
```

## 2. Defining the Target Grid

You need to define the grid you want to regrid to. This can be another dataset's grid, or you can create a new one. Here, we create a regular lat/lon grid:

```python
import numpy as np

target_grid = xr.Dataset({
    'lat': (['lat'], np.arange(-90, 90.1, 1.0)),
    'lon': (['lon'], np.arange(0, 360, 1.0)),
})
```

### Visualizing the Target Grid

```python
plt.figure()
plt.scatter(target_grid['lon'], target_grid['lat'], s=1)
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.title('Target Grid Points')
plt.show()
```

## 3. Regridding with xESMF

xESMF supports several regridding methods:

### Choosing a Regridding Algorithm

The choice of algorithm depends on your data and scientific goals:

- **Bilinear**: Uses weighted averages of the four nearest grid points. It is smooth and works well for continuous variables (e.g., temperature, pressure). However, it does not conserve the total sum of the variable, so it is not suitable for fluxes or quantities where conservation is important.

- **Conservative**: Ensures that the integral (sum) of the variable is preserved during regridding. This is essential for variables like precipitation, runoff, or any fluxes. It requires both source and target grids to define cell boundaries (i.e., grid must be defined by cell centers and edges). It can be sensitive to missing values (NaNs), which may cause the output to be NaN if any input cell is NaN.

- **Conservative-normed**: Similar to conservative, but normalizes the weights so that if some source cells are NaN, the valid part of the cell is still used. This is especially useful for satellite data or observational products with missing values, as it avoids propagating NaNs unnecessarily. Use this when you want conservation but need to handle missing data robustly.

- **Nearest_s2d / nearest_d2s**: Assigns the value of the nearest source (or destination) grid cell. This is fast and preserves original values, but can introduce blocky artifacts. Use for categorical data (e.g., land/sea masks, land cover types) or when you want to avoid interpolation.

- **Patch**: A higher-order method that can provide smoother results for some variables, but is less commonly used and more computationally intensive.

#### Summary Table

| Algorithm            | Preserves Integrals | Handles NaNs Well | Smooth | Use For                        |
|----------------------|--------------------|-------------------|--------|-------------------------------|
| bilinear             | No                 | Moderate          | Yes    | Continuous fields              |
| conservative         | Yes                | No                | No     | Fluxes, precipitation         |
| conservative-normed  | Yes                | Yes               | No     | Fluxes with missing data      |
| nearest_s2d/d2s      | No                 | Yes               | No     | Categorical, masks            |
| patch                | No                 | Moderate          | Yes    | Advanced, smooth fields       |


### Example: Bilinear Regridding

```python
import xesmf as xe

regridder = xe.Regridder(ds, target_grid, 'bilinear')
regridded = regridder(ds['your_variable'])
regridded.to_netcdf('output_bilinear.nc')
```

### Example: Conservative Regridding

```python
regridder_cons = xe.Regridder(ds, target_grid, 'conservative')
regridded_cons = regridder_cons(ds['your_variable'])
regridded_cons.to_netcdf('output_conservative.nc')
```

### Example: Conservative-normed Regridding

The **conservative-normed** method is designed to handle missing values (NaNs) more robustly than standard conservative regridding. In the standard conservative method, if any part of a source cell is NaN, the entire destination cell may become NaN. The conservative-normed method normalizes the weights so that only the valid (non-NaN) fraction of the source cell contributes to the destination cell, preventing unnecessary propagation of NaNs.

#### Using a 'mask' Layer

To take full advantage of conservative-normed, you should provide a `mask` variable in your xarray dataset. This mask should be a DataArray with the same shape as your data, where valid data points are marked as 1 (or True) and missing/invalid points as 0 (or False). xESMF will use this mask to determine which parts of the grid are valid during regridding.

Example of adding a mask:

```python
import numpy as np

# Suppose ds['your_variable'] contains NaNs for missing data
mask = (~np.isnan(ds['your_variable'])).astype(int)
ds['mask'] = (ds['your_variable'].dims, mask)

# Now use conservative-normed
regridder_normed = xe.Regridder(ds, target_grid, 'conservative_normed')
regridded_normed = regridder_normed(ds['your_variable'])
regridded_normed.to_netcdf('output_conservative_normed.nc')
```

If you do not provide a mask, xESMF will infer it from the NaN pattern in your data, but explicitly providing a mask is more robust and recommended for complex or irregular missing data patterns.

For more, see the [xESMF documentation on masking](https://xesmf.readthedocs.io/en/latest/notebooks/Masking.html).

### Example: Nearest Neighbor Regridding

```python
regridder_nn = xe.Regridder(ds, target_grid, 'nearest_s2d')
regridded_nn = regridder_nn(ds['your_variable'])
regridded_nn.to_netcdf('output_nearest.nc')
```

## 4. Considerations


### Working with Large Datasets

Regridding large datasets (e.g., high-resolution satellite data or long time series) can be memory- and compute-intensive. Here are some tips to improve performance:

- **Use Dask for Chunking**: xarray and xESMF support dask arrays, which allow you to process data in chunks and parallelize operations. Open your dataset with chunking:

    ```python
    ds = xr.open_dataset('input_data.nc', chunks={'time': 10, 'lat': 100, 'lon': 100})
    # Adjust chunk sizes to fit your memory and data shape
    ```

- **Saving and Reusing Regridding Weights**: When you create a regridder in xESMF, it computes a weight matrix that maps the source grid to the target grid. This computation can be slow for large grids, but you can save the weights to a file and reload them later for faster repeated regridding.

    Example:

    ```python
    # First time: compute and save weights
    regridder = xe.Regridder(ds, target_grid, 'bilinear', filename='my_weights.nc')

    # Next time: reuse the saved weights (much faster)
    regridder = xe.Regridder(ds, target_grid, 'bilinear', filename='my_weights.nc', reuse_weights=True)
    ```

    This is especially useful when you need to regrid many variables or process data in chunks, as you only need to compute the weights once.

- **Parallel Processing**: If you have access to a cluster or multicore machine, dask can distribute the computation. Set up a dask cluster for even faster processing.

- **Reduce Data Size**: If possible, subset your data in time or space before regridding, or use coarser grids for exploratory analysis.

- **Monitor Memory Usage**: Large regridding operations can use a lot of RAM. Monitor your system and adjust chunk sizes or process data in smaller batches if needed.

For more details, see the [xESMF documentation on dask and performance](https://xesmf.readthedocs.io/en/latest/parallel.html).

## 5. Visualizing the Results

```python
import matplotlib.pyplot as plt

regridded.plot()
plt.title('Regridded Data (Bilinear)')
plt.show()
```

### Comparing Input and Output

You can compare the original and regridded data side by side:

```python
fig, axs = plt.subplots(1, 2, figsize=(12, 5))
ds['your_variable'].plot(ax=axs[0])
axs[0].set_title('Original Data')
regridded.plot(ax=axs[1])
axs[1].set_title('Regridded Data (Bilinear)')
plt.tight_layout()
plt.show()
```

For more advanced analysis, you can plot the difference:

```python
diff = regridded - ds['your_variable'].interp_like(regridded)
diff.plot()
plt.title('Difference After Regridding')
plt.show()
```

## 6. References

- [xESMF Documentation](https://xesmf.readthedocs.io/)
- [xarray Documentation](https://docs.xarray.dev/)

---

This tutorial should help you get started with regridding 2D NetCDF datasets in Python using xESMF. Adjust the code to your specific data and needs!
