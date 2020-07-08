[TOC]

[HugeCTR](https://github.com/NVIDIA/HugeCTR)��Ӣΰ���ṩ��һ�ָ�Ч��GPU��ܣ�רΪ����ʣ�CTR������ѵ������ơ�

OneFlow�Ա�HugeCTR���Wide & Deep ѧϰ���磨WDL)��OneFlow-WDL����ʵ����ģ�Ͳ�����ϡ����£���8��12G TitanV�ķ�������ʵ��֧�ֳ���4�ڵĴʱ��С����������û����ʧ��С�ʱ������൱��

���Ľ������ʹ��OneFlow-WDL�������ѵ�����Լ�һЩѵ�������������

## ������׼��
����OneFlow-WDL��Ҫ�а�װ��OneFlow��python����������װ��[scikit-learn](https://scikit-learn.org/stable/install.html)��
### ���Ҫ��
- python 3.x���Ƽ���
- OneFlow 0.x
- scikit-learn

### ����׼��
����׼����һ��С��[�������ݼ�](ofrecord-todo)���������ؽ��м򵥲��ԡ�

���߲ο�[��ʹ��Spark����WDL���ݼ���](create_WDL_ofrecord_with_spark.md)�еĲ��裬��CriteoLabs��������ԭʼ���ݼ���������OneFlow����Ҫ��OFRecord��ʽ�����ݼ���

### OneFlow-WDL�ű�
OneFlow-WDL�ű�ֻ��һ���ļ�`wdl_train_eval.py`�����[����](wdl_train_eval.py)���ء�

## ����OneFlow-WDL�ű�
```
EMBD_SIZE=1603616
DATA_ROOT=/path/to/wdl/ofrecord
python3 wdl_train_eval.py \
  --train_data_dir $DATA_ROOT/train \
  --train_data_part_num 256 \
  --train_part_name_suffix_length=5 \
  --eval_data_dir $DATA_ROOT/val \
  --eval_data_part_num 256 \
  --max_iter=300000 \
  --loss_print_every_n_iter=1000 \
  --eval_interval=1000 \
  --batch_size=16384 \
  --wide_vocab_size=$EMBD_SIZE \
  --deep_vocab_size=$EMBD_SIZE \
  --gpu_num 1
```
ͨ�����ú����ݼ���λ��`DATA_ROOT`�������shell�ű��Ϳ��Ա�ִ���ˣ������Ļ���ܹ�����������ƵĽ�����ͱ�ʾ�Ѿ���ȷ���С�
```
1000 time 2020-07-08 00:28:08.066281 loss 0.503295350909233
1000 eval_loss 0.4846755236387253 eval_auc 0.7616240146992771
2000 time 2020-07-08 00:28:11.613961 loss 0.48661992555856703
2000 eval_loss 0.4816856697201729 eval_auc 0.765256583562705
3000 time 2020-07-08 00:28:15.149135 loss 0.48245503094792364
3000 eval_loss 0.47835959643125536 eval_auc 0.7715609382514008
4000 time 2020-07-08 00:28:18.686327 loss 0.47975033831596375
4000 eval_loss 0.47925308644771575 eval_auc 0.7781267916810946
```
## ���Խ����˵��
������һ̨��8��12G�Դ��TitanV�ķ������϶�OneFlow-WDL������һ����ԣ���ʹ��HugeCTR�ṩ��docker��������ͬ�������Ĳ��ԡ�

### ��GPU���ܲ���
��Ҫ����Ŀ������batch size = 16384������£�������ͬGPU��������ÿ�����ε�ƽ��ʱ�ӣ�latency����
����������7��1024�񾭵�Ԫ�����ز㡣

�������ͼ��

![image](imgs/fixed_batch_size_latency.png)

����ͬʱ��¼�ˣ�����ʱʵ�����ռ���Դ�Ĵ�С���������ͼ��

![image](imgs/fixed_batch_size_memory.png)

�ۺ�������������1����8����OneFlow-WDL��ռ�ý��ٵ��Դ������£��ٶ�Ҫ��HugeCTR�졣

### batch size=16384ÿ�����࿨���ܲ���
��Ҫ����Ŀ�����ڱ�֤ÿGPU������16384batch size����£�ʹ��1��8GPU������ѵ��ÿ�����ε�ƽ��ʱ�ӣ�latency����
����������7��1024�񾭵�Ԫ�����ز㡣

�������ͼ��

![image](imgs/scaled_batch_size_latency.png)

����ͬʱ��¼�ˣ�����ʱʵ�����ռ���Դ�Ĵ�С���������ͼ��

![image](imgs/scaled_batch_size_memory.png)

�ۺ����������������ſ��������ӣ�ʱ�����ӣ�OneFlow-WDL��ռ�ý��ٵ��Դ������£��ٶ�Ҫ��HugeCTR�죻��Ϊÿ����֤16384 batch size��OneFlowÿ��ռ�õ��ڴ沢�������仯��

### ��GPU����ͬbatch size���ܲ���
��Ҫ����Ŀ������һ��GPU������£�������ͬbatch sizeÿ�����ε�ƽ��ʱ�ӣ�latency����
����������2��1024�񾭵�Ԫ�����ز㡣

�������ͼ��

![image](imgs/fixed_batch_size_latency_1gpu.png)

### ����ʱ����
OneFlow-WDL������������Embedding Table��
- `wide_embedding` ��С��vocab_size x 1
- `deep_embedding` ��С��vocab_size x 16

HugeCTR�дʱ��С��vocab_size����1603616�����Ǵ�3200000��ʼ����һֱ��֧��4�ڵĴʱ��С���������ͼ��

![image](imgs/big_vocab_table_2x1024.png)
![image](imgs/big_vocab_table_7x1024.png)

�����ͼ�У���ɫ����������ѵ����ƽ��ʱ�ӣ�latency������ɫ���ߴ���GPU�Դ��ռ�á�

���ۣ����Ŵʱ��С�������ڴ���֮���󣬵�latencyû�����Եı仯��

### �����Բ���1
����ѡȡ��batch size=512�������������ܵĲ��ԡ�

��������ͼ�ǣ�ǰ500���Ľ����ÿһ��ѵ��������֤����ѡȡ20����¼������֤��ͼ�е����߷ֱ���loss��AUC��
![image](imgs/eval_auc_loss_500iters.png)

���ۣ�AUCѸ�پ�������������0.75��

### �����Բ���2
�������Բ���1ͬ�����������һ����ÿѵ��1000����ӡѵ��loss��ƽ��ֵ��Ȼ��ѡȡ20����֤�����ݽ�����֤��һ��ѵ��30�򲽣�������£�

![image](imgs/train_eval_auc_loss.png)

�����������
1. ��ɫ��train loss�������������µ�̨�ף���Ϊ����ѵ������36674623�����ݣ�batch_size=512������£����71630���͹����������ݼ���һ��epoch����30�򲽾Ͱ�ѵ�����ݼ�����4�ζ࣬��ɫ���ߵ�̨��ӡ֤����Щ��OneFlow��ѵ��������֧�����ݵĴ��ң�ÿ�����ݼ�������������һ��֮�����ݻᱻ���´��ң����ٹ���ϡ�
2. ��ɫ����������֤��loss����ǰ����epoch��ʱ����������½������ƣ��ӵ�����epoch��ʼ��loss��ʼ�����������ƣ������Ѿ�������ˡ�
3. ��ɫ����֤����AUC��AUCҲ���ڵڶ���epoch��ʱ��ﵽ�˷�ֵ��������0.8�����漸��epoch�Ϳ�ʼ�½���
