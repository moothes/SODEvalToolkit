# Evaluation Scripts for Salient Object Detection

The repo contains **Matlab** script to evaluate salient object detection algorithms.

## Install

```
git clone https://github.com/lartpang/SODEvalToolkit.git
```

## HOW TO USE

_The code should be easy to use._ It allows any numbers of algorithms to be evaluated on a single dataset at one time.

1. Get the `.mat` file of the results: Just run `evaluate_models.m` in matlab.
2. Plot these value: You can run `draw_curves.m` in matlab.
3. Export the results in the folder `Results` by running `mat2excel.m`
4. (Optional) Change the excel's data to latex code with <http://www.tablesgenerator.com/>

**NOTE**:
* Please read the configuration items carefully when using the code to avoid unnecessary erors. If you have more questions, you can create a new issue.
* It is really convinient to export the figure into a pdf and use it in the latex document. But, when you use the pdf generated from the latex document as the conference's submittion, and wish it to pass the PDF check of *IEEE PDF eXpress*, maybe, you will recive the report: `Font Times-Bold, Times-Roman, Times-Roman*1 is not embedded...`. At this time, you can use *Adobe Acrobat* to embed fonts in the figure pdf and then, generate the pdf from *LaTeX*, again. Everything's gonna be all right.

More information can be seen in the comment text of the corresponding file.

### setup the dataset

```
%% ------------ setup the dataset under evaluation ------------
Dataset.datasetName = 'DUTS';
gtMapThreshold = 0.5;  % 原始作者使用的阈值是0.1，这里提取出来作为可设置的参数
fprintf('Executing dataset: \n-----Name: %s\n', Dataset.datasetName);
fprintf('-----GtMap threshold: %.2f\n', gtMapThreshold)

% setup the ground truth paths，根据实际情况设置，注意要加路径分隔符
Dataset.GTdir = ['/home/lart/Datasets/RGBSaliency/',Dataset.datasetName,'/Test/Mask/'];
fprintf('-----Number of Images: %d\n', length(dir([Dataset.GTdir,'*']))-2);

% setup the path to save the results
Dataset.savedir                         = [ './Results/' , Dataset.datasetName , '/' ];
if ~exist(Dataset.savedir,'dir')
    mkdir(Dataset.savedir);
end
```

You can change `Dataset.GTdir` to `/path/to/your/GTfiles/` where your **ground truth** are. (like `DUTS`, `ECSSD`...)

The default folder to save the results is `Dataset.savedir`, generally speaking, you needn't change it.

### select results to be evaluate

```
set_format = false;
[alg_params, runNum, path, cancel]...
                                = select_Alg(Dataset.datasetName, set_format);
if cancel == 1
    plotMetrics = 'User canceled during selecting new algorithms to evaluate!\n';
    return;
end
```
The function `select_Alg` allows users to open the folder that contains the results of algorithms to be evaluated. The default folder is `./SaliencyMaps/Datasetname/`. If you put the results in the default folder, just click 'open'.

It also allows user to set the name format of saliency maps, such as _prefix_, _postfix_, and _ext_ (It looks like: '_prefix_ NameOfImage _postfix_ ._ext_'). Set `set_format=true` to use it. Default is 'NameOfImage.png', i.e. _prefix_='', _postfix_='', _ext_='png'.

### evaluate the results

```
%% ------------ evaluate the results ------------
metrics                         = {};
if runNum ~= 0
    alg_dir_struct              = candidateAlgStructure( alg_params,path );

    % perform evaluation
    fprintf('\nPerforming evaluations...\n');
    metrics                     = performCalcu(Dataset,alg_dir_struct);

    % save the resuls
    savematfiles(metrics,alg_params,Dataset.savedir);
    fprintf('\nResults are saved in %s\n', Dataset.savedir);
end
```

You can load the `.mat` files to draw the curves you need.

 Metrics:

- [x] Pre
- [x] Recall
- [x] mean F-measure and the corresponding precision and reall
- [x] S-measure
- [x] MAE
- [x] F-measure Curve
- [x] max F-measure
- [x] IoU Curve
- [x] IoU at max F-measure
- [x] max IoU
- [x] mean IoU
- [x] E-measure
- [x] weighted F-measure

## Cite This Repo

**If you find this code useful in your research, please consider citing:**

```
@article{sal_eval_toolbox,
    Author = {Mengyang Feng},
    Title = {Evaluation Toolbox for Salient Object Detection.},
    Journal = {https://github.com/ArcherFMY/sal_eval_toolbox},
    Year = {2018}
}
```
