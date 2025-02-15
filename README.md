# Single Molecule Reconstruction Package

A Python package for creating Gaussian reconstructions of single molecule microscopy data, with support for masked regions and uniform distribution modeling.

## Features

- Generate 2D Gaussian reconstructions from single molecule localizations
- Support for masked regions of interest
- Flexible pixel size scaling and error handling
- Uniform distribution modeling within masked regions
- Multiple image format support (PNG, JPG, TIF, SVG)
- Coordinate conversion between reconstruction and original space

## Installation

```bash
pip install smrecon
```

## Quick Start

```python
from smrecon import SMReconstruction, ImageFormat

# Initialize reconstruction
reconstructor = SMReconstruction(
    img_dims=(512, 512),  # Original image dimensions
    pixel_size=130,       # Original pixel size in nm
    rescale_pixel_size=10 # Target pixel size in nm
)

# Create reconstruction from localizations
localizations = np.array([[x1, y1], [x2, y2], ...])  # Your localization data
errors = np.array([e1, e2, ...])  # Localization errors

image = reconstructor.make_reconstruction(
    localizations=localizations,
    localization_error=errors
)

# Save the result
reconstructor.save_image(
    path="output/",
    name="reconstruction",
    fmt=ImageFormat.TIF
)
```

## Advanced Usage: Masked Reconstruction

```python
from smrecon import SMReconstructionMasked, MaskedReconstructionConfig

# Create custom configuration
config = MaskedReconstructionConfig(
    bounding_box_padding=5,
    mask_threshold=255,
    gaussian_scale_factor=5.0
)

# Initialize masked reconstruction
masked_reconstructor = SMReconstructionMasked(
    img_dims=(512, 512),
    pixel_size=130,
    rescale_pixel_size=10,
    config=config
)

# Create masked reconstruction
masked_image = masked_reconstructor.make_reconstruction(
    localizations=localizations,
    localization_error=errors,
    masked_img=your_mask_array,
    uniform=False  # Set to True for random within mask, number of localizations will be placed in the mask region uniformly.
)
```

## API Reference

### Main Classes

#### `SMReconstruction`
Base class for single molecule reconstruction.

#### `SMReconstructionMasked`
Extended class supporting masked regions and uniform distributions.

### Configuration Classes

#### `ReconstructionConfig`
```python
class ReconstructionConfig:
    bounding_box_padding: int = 5
    random_seed: int = 666
    gaussian_scale_factor: float = 5.0
```

#### `MaskedReconstructionConfig`
```python
class MaskedReconstructionConfig(ReconstructionConfig):
    mask_threshold: int = 255
    uniform_distribution: bool = False
```

### Enums

#### `ImageFormat`
Supported image formats:
- PNG
- JPG
- TIF
- SVG

#### `ConversionType`

Converting between the reconstruction space coordinates and the original space (and vice versa).
```python
# Initialize masked reconstruction
masked_reconstructor = SMReconstructionMasked(
    img_dims=(512, 512),
    pixel_size=130,
    rescale_pixel_size=10,
    config=config
)

# Create masked reconstruction
masked_image = masked_reconstructor.make_reconstruction(
    localizations=localizations,
    localization_error=errors,
    masked_img=your_mask_array,
    uniform=False  # Set to True for uniform distribution within mask
)

xycoord, spatialcoord = masked_image.coordinate_conversion([x_i, y_i], r_i, conversion_type)
# x_i, y_i are the spacial coordinates and are affected by bounding box and rescaling
# r_i is a distance and only affected by rescaling.
# first argument is always a spatial coordinate and second is always distance.
```

Coordinate conversion types:
- RC_TO_ORIGINAL # recon -> original space
- ORIGINAL_TO_RC # original -> recon space

conversion_type takes values of an enum:
```python
class ConversionType(str, Enum):
    """Types of coordinate conversion."""

    RC_TO_ORIGINAL = "RC_to_Original"
    ORIGINAL_TO_RC = "Original_to_RC"
```

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.
