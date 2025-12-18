# cell-segmentation-using-cellpose
Cellpose is a deep learning network that is generalizable across diverse cell microscopy images and automatically segments single cells to improve throughput and reduce inter-human biases. This study aims to validate Cellpose for autofluorescence imaging, specifically using multiphoton intensity images of NAD(P)H.

# Cell Segmentation
Cellpose 2.0 introduces a residual block for cell segmentation, predicting input image and generating three matrices for horizontal and vertical gradients, alongside pixel cell detection. The following step involves manually fixing the cell mask using Adobe Photoshop.

## 1. Cell Segmentation by Cellpose 2.0
Cellpose 2.0 is a new package that includes a diverse collection of pre-trained models and a human-in-the-loop pipeline for rapid prototyping of new custom models. 

Generated cell mask using our train model. 

    $ python3 -m cellpose --dir <./input_dir> --pretrained_model <./train_model_dir> --save_tif --verbose


## 2. Image processing by ImageJ
The cell mask obtained from Cellpose is further processed using ImageJ to generate images with clearly visible features.


## 3. Manually Fixed by Adobe Photoshop
Fixed part of the sample to be analyzed further.


## 4. Extract Cellbin Expression
After obtaining the cell mask, we generated cellbin GEF file using CellCut and the corresponding BGEF (SN.raw.gef). This step will extract the cellbin expression data using the data of stereo-seq raw GEF and the mask file. Stereo-seq Analysis Workflow (SAW) software suite is a set of pipelines bundled to map sequenced reads to their spatial location on the tissue section, quantify spatial feature expression, and visually present spatial expression distribution. SAW processes the data from the Stereo-seq sequencing platform, combined with microscope images, to generate spatial feature expression matrices.
    
    from stereo.tools.cell_cut import CellCut
    
    cgef_out_dir = "./cell_cut_out"
    bgef_path = "./SN.raw.gef"
    mask_path = "./SN_mask.tif"

    cc = CellCut(cgef_out_dir=cgef_out_dir)
    out_path = cc.cell_cut(bgef_path=bgef_path, mask_path=mask_path)

## 5. Generate GEM File
Convert GEF to GEM file using SAW V6 software (currently SAW has been upgraded to V8, you can adjust it). Stereo-seq Analysis Workflow (SAW) software suite is a set of pipelines bundled to map sequenced reads to their spatial location on the tissue section, quantify spatial feature expression, and visually present spatial expression distribution. SAW processes the data from the Stereo-seq sequencing platform, combined with microscope images, to generate spatial feature expression matrices.

    $ export APPTAINER_BIND="./input_file_dir"
    $ singularity exec SAW_v6.1.sif cellCut view
        -i /path/to/input/{SN}.raw.cellbin.gef \
        -s {SN} \
        -o /path/to/output/{SN}.raw.cellbin.gem
        -d /path/to/output/03.count/{SN}.raw.gef
        
Generate the GEM file using Perl.

    $ perl file.pl <input> <output> 

