如果进行csv表格数据的转换，推荐使用包pandas_tfrecords
# 效率对比
TFRecord和CSV，都是用TFDataset的方式进行读取，对比效率
但是由于表格数据需要经常变换特征列，数据不想nlp或者图像那么稳定，进行tfrecord的这一步转换成本比较大
但是deepctr的样例中还是使用了tfrecord
如果直接用csv的话，seq怎么处理，tf.data.experimental.make_csv_dataset会直接将csv中的list识别为一个字符串

# 转换
1. 将原始的pandas文件拆分成很多份分别存储，然后起多进程根据文件名去读取并处理。不能把数据作为多进程的参数来传递，极其浪费内存
2. 根据pd.DataFrame中的数据类型自动推断tfrecord中应该保存的类型，所以在存储时，需要先确保DataFrame中每列的数据类型，


3. 用spark直接将hive表格转换成tfrecord
# 解析
1. 单值FixedLenFeature
2. 列表(VarLenFeature或者historySeq)需要用FixedLenSequenceFeature

```
def file_based_input_fn_builder(
    input_files,
    batch_size=1,
    is_training=True,
    drop_remainder=False
):
    name_to_context_features = OrderedDict()
    name_to_sequence_features = OrderedDict()
    name_to_context_features["clickLabel"] = tf.FixedLenFeature([1], tf.int64)
    name_to_context_features["conversionLabel"] = tf.FixedLenFeature([1], tf.int64)
    for feature_name in sparse_fixlen_feature_names:
        name_to_context_features[feature_name] = tf.FixedLenFeature([1], tf.string)
    for feature_name in dense_fixlen_feature_names:
        name_to_context_features[feature_name] = tf.FixedLenFeature([1], tf.float32)
        
    for feature_name in sparse_varlen_feature_names:
        name_to_sequence_features[feature_name] = tf.FixedLenSequenceFeature([], tf.string,allow_missing=True)


    def _decode_record(record, name_to_context_features, name_to_sequence_features):
        """Decodes a record to a TensorFlow example."""
        context, sequence = tf.parse_single_sequence_example(record, context_features=name_to_context_features, sequence_features=name_to_sequence_features)
        
        context.update(sequence)
        example = context

        for name in list(example.keys()):
            t = example[name]
            if t.dtype == tf.int64:
                t = tf.to_int32(t)
            example[name] = t
        mylabel = example.pop('clickLabel')

        return example, mylabel

    def input_fn(params=None):
        dataset = tf.data.TFRecordDataset(input_files)
        if is_training:
            dataset = dataset.repeat()
            dataset = dataset.shuffle(buffer_size=500)

        dataset = dataset.apply(
            tf.contrib.data.map_and_batch(
                lambda record: _decode_record(record, name_to_context_features, name_to_sequence_features),
                batch_size=batch_size,
                drop_remainder=drop_remainder))

        return dataset

    return input_fn


example = file_based_input_fn_builder(
    ['my.tfrecords'])()
```