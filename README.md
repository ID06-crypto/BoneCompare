# ICP Automation

## This project is made to automate the process of comparing 3d vector point clouds of bones

# The main issue being addressed here, aside from the loop and parallelization of doing this process for many samples is the alignment of the two bones in question. Because we do not know the orientation or scale of either of the bones (stored in stl files), we must use some cool tricks to get them basically aligned, and then we can use the ICP function for the final adjustments.

## Coarse Bone Alignment

1) 
