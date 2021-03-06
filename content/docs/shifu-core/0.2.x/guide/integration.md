---
title: Integration
---

Step 7: Integration
===================

## Create ModelRunner

There are 3 ways to create a ``ModelRunner``:

Method 1:

    #!java
    public ModelRunner(
                ModelConfig modelConfig,
                List<ColumnConfig> columnConfigList, 
                String[] header, 
                String dataDelimiter,
                List<BasicML> models) 

Method 2:

    #!java
    public ModelRunner(
                List<ColumnConfig> columnConfigList, 
                List<BasicML> models)
        
Method 3:

    #!java
    public ModelRunner(
                List<ColumnConfig> columnConfigList, 
                List<BasicML> models, 
                double stdDevCutoff)
             
where ``modelConfig`` can be loaded by 

    #!java
    import com.paypal.shifu.util.CommonUtils;
    import com.paypal.shifu.container.obj.ModelConfig;
    import com.paypal.shifu.container.obj.RawSourceData.SourceType;

    ModelConfig modelConfig = CommonUtils.loadModelConfig(
                "path/to/ModelConfig.json",
                SourceType.LOCAL);

similarly load ``columnConfigList``

    #!java
    List<ColumnConfig> columnConfigList = CommonUtils.loadColumnConfigList(
                "path/to/ColumnConfig.json", 
                SourceType.LOCAL);

load the ``models``

    #!java
    public static List<BasicML> loadBasicModels(final String modelsPath, final ALGORITHM alg)

Currently ``NN`` is supported ALGORITHM values.

If you use Method 1 to construct ModelRunner, ``header`` and ``dataDelimiter`` should be easy to load.

    #!java
    ModelRunner modelRunner = new ModelRunner(modelConfig, columnConfigList, header, delimiter, models);

## Compute

The ``ModelRunner`` is ready to run. There are 3 ways to compute the score:

#### RawDataMap

Send a HashMap to modelRunner, where the key is the column name, and value is the data

    #!java
    Map<String, String> rawDataMap = new HashMap<String, String>();
    ...
    CaseScoreResult result = modelRunner.compute(rawDataMap);

#### RawString

<div class='alert alert-warning'>
Note: only supported when using Method 1 to construct ModelRunner
</div>

Load the raw string from data file, internally it will converted to a HashMap based on the header and delimiter.

    #!java
    String rawString = "1,2,3,4,5,6";

    CaseScoreResult result = modelRunner.compute(rawString);

#### (Pig) Tuple

<div class='alert alert-warning'>
Note: only supported when using Method 1 to construct ModelRunner
</div>

Similar to the RawString, it will be internally converted to a HashMap based on the header

    #!java
    Tuple tuple;
    ...
    CaseScoreResult result = modelRunner.compute(tuple);

## Returned Value

The ``compute`` method returns a ``CaseScoreResult`` object. Where it stores the following data:

    #!java
    private Map<String, String> rawDataMap;
    private List<Integer> scores;
    private int maxScore;
    private int minScore;
    private int avgScore;
    private int medianScore;

For example, to get the average score:

    #!java
    int score = result.getAvgScore(); 

The ``length`` of the ``scores`` depends on the number of ``models``, you are free to use whichever score that makes the most sense.