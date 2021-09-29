# Agscan3D
This repository provides links to the code and datasets used in the paper:

### [Canopy Density Estimation in Perennial Horticulture Crops Using 3D Spinning Lidar SLAM](https://onlinelibrary.wiley.com/doi/abs/10.1002/rob.22006)

If you find this work useful for your research, please consider citing:

```
@article{lowe2021canopy,
	author = {Lowe, Thomas and Moghadam, Peyman and Edwards, Everard and Williams, Jason},
	title = {Canopy density estimation in perennial horticulture crops using 3D spinning lidar SLAM},
	journal = {Journal of Field Robotics},
	volume = {38},
	number = {4},
	pages = {598-618},
	doi = {https://doi.org/10.1002/rob.22006},
	year = {2021}
}
```


Datasets used in this work are available at:

These datasets can be cited as "Lowe, Tom; Moghadam, Peyman; Edwards, Everard; Williams, Jason; Brosnan, Stephen; Haddon, David; Dungavell, Ross (2021): [AgScan3D Viticulture Datasets. v1.](https://doi.org/10.25919/gbvy-yz23) CSIRO. Data Collection. https://doi.org/10.25919/gbvy-yz23"

If you use the dataset for your research, please consider citing the dataset as well:

```
@article{agscan3d2021,
        author = {Lowe, Thomas and Moghadam, Peyman and Edwards, Everard and Williams, Jason and Brosnan, Stephen and Haddon, David and Dungavell, Ross},
        title = {AgScan3D Viticulture Datasets. v1.},
        journal = {CSIRO. Data Collection},
        doi = {https://doi.org/10.25919/gbvy-yz23},
        year = {2021}
}
```


There is a library available for extracting density images from this dataset, it is available here: https://github.com/csiro-robotics/raycloudtools/


To build the code, follow the instructions in the README file. In order to remove the uneven ground, the QHULL library is required, follow the instructions in the README file for installing QHULL and enable the WITH_QHULL flag.

In order to extract density information, we recommend following a sequence of commands similar to the following:

```
rayalign vineyard.ply                      # aligns it along the row direction
raywrap vineyard_aligned.ply upwards 0.001 # generate a ground mesh assuming a low curvature
raysplit vineyard_aligned.ply vineyard_aligned_mesh.ply distance 1 # cut out 1 m above the ground
mv vineyard_aligned_inside.ply vines.ply
rayrender vines.ply top density_rgb        # render to an image file as a red-green-blue heatmap
```

where vineyard.ply is the name of any of the ray clouds in the data examples. 

This will generate a red-green-blue heatmap of the estimated leaf area density from the top viewpoint. This heatmap is scaled to the range of densities in the data, so to get absolute real world values per pixel (leaf area density in m^2 / m^2) then output to a greyscale .hdr file:

```rayrender vines.ply top density -o vines.hdr```

If the process is too slow, or runs out of memory on your system, then you can reduce the ray cloud to a smaller subsampling of the points, like so:

```raydecimate vineyard.ply 10 cm```

To split the vineyard by row, you can use the ```raysplit``` command as a grid with two very large dimensions:

```raysplit vines.ply grid 3,1000,10000```

where 3 is the distance between rows in metres. If the vines aren't centred in these cuboids then you can shift the vines manually, e.g: ```raytranslate vines.ply 0.2,0,0``` or adjust the rotation around the vertical axis, e.g: ```rayrotate vines.ply 0,0,5``` (five degrees in this case).

