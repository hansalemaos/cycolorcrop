# Crops an image based on allowed colors

![](https://github.com/hansalemaos/cycolorcrop/blob/main/imagecrop.png?raw=true)

## pip install cycolorcrop


> [!CAUTION]
> **Cython and a C++ compiler must be installed!
> The function will be compiled the first time you import it!**


## How to use

```sh
    Crops an image based on allowed colors using external C++ routines.

    This function determines the cropping boundaries of an image by searching for specified allowed colors.
    It leverages external functions from "colorcrop.hpp" to compute the starting and ending coordinates along
    both the x and y axes. The function accepts an image and an array of colors, ensures proper type and
    memory alignment, and returns the cropped image along with the crop coordinates.

    Parameters
    ----------
    img : numpy.ndarray
        The input image as a NumPy array with shape (height, width, channels). The channels must be either 3 (RGB)
        or 4 (RGBA). If the image has 3 channels, an alpha channel with full opacity (255) will be appended.
    search_for_colors : numpy.ndarray
        A 2D NumPy array where each row represents a color in RGB or RGBA format. If provided with 3 channels,
        an alpha channel with full opacity (255) is added to each color.
    parallel : bool, optional
        Flag indicating whether to use parallel processing for computing crop coordinates. If True, the function
        uses a parallel loop (with Cython's prange) to compute the boundaries. Default is False.

    Returns
    -------
    list
        A two-element list where:
          - The first element is the cropped image (NumPy array).
          - The second element is a dictionary containing the crop boundaries:
                {
                    "start_x": int,  # Starting x-coordinate
                    "end_x": int,    # Ending x-coordinate
                    "start_y": int,  # Starting y-coordinate
                    "end_y": int     # Ending y-coordinate
                }

    Raises
    ------
    ValueError
        If either the image or the search_for_colors array is empty, or if the channel configuration is not supported
        (only RGB/RGBA images are allowed).

    Notes
    -----
    - The function ensures that both input arrays are of type np.uint8 and are C-contiguous and aligned.
    - When necessary, the image and search_for_colors arrays are copied to meet these requirements.
    - If parallel processing is enabled, the crop coordinates are computed using a parallel loop with
      the external function 'find_coord'.
    - The actual cropping is performed by slicing the original image based on the computed coordinates.
    - External functions used (from "colorcrop.hpp"):
          find_start_y, find_end_y, find_start_x, find_end_x, and find_coord.
    - RGBA is faster than RGB
```

## Usage example

```py
from cycolorcrop import crop_image
import cv2
import numpy as np

img = cv2.imread(
    r"C:\ProgramData\anaconda3\envs\image2crop.png", cv2.IMREAD_UNCHANGED
)  # (RGB or RGBA)
search_for_colors = np.array(
    [[255, 255, 255]], dtype=np.uint8
)  # more than one color can be specified (RGB or RGBA)

croppedimg, crop_coords1 = crop_image(img, search_for_colors, parallel=False)
cv2.imwrite(r"C:\ProgramData\anaconda3\envs\image2cropped_normal.png", croppedimg)

croppedimg2, crop_coords2 = crop_image(img, search_for_colors, parallel=True)
cv2.imwrite(r"C:\ProgramData\anaconda3\envs\image2cropped_parallel.png", croppedimg2)

```