https://github.com/yulab2021/TandemMod/tree/master
#安装
sudo apt-get install zlib1g-dev #sudo yum install zlib-devel
mamba install pandas==1.3.5 patsy==0.5.3 packaging==23.1  progressbar33==2.4 python-dateutil==2.8.2  pytz==2023.3 statsmodels==0.10.0 tqdm==4.65.0 typing-extensions==4.6.3
pip install future==0.18.3
pip install mappy==2.26 ont-fast5-api==4.1.1 torch==1.9.1 --index-url https://pypi.tuna.tsinghua.edu.cn/simple #指定镜像源安装
conda install -c bioconda ont-tombo==1.5.1

## 已经安装完成的 six==1.16.0 patsy==0.5.3 packaging==23.1

#ont-guppy-cpu安装，建议用docker 6.1.5
https://hub.docker.com/r/chrishah/guppy/tags

https://mirror.oxfordnanoportal.com/software/analysis/ont-guppy-cpu_3.3.0_linux64.tar.gz
tar -xzvf ont-guppy-cpu_3.3.0_linux64.tar.gz

https://community.nanoporetech.com/docs/prepare/library_prep_protocols/Guppy-protocol/v/gpb_2003_v1_revax_14dec2018/linux-guppy
https://help.nanoporetech.com/en/articles/6628042-how-do-i-install-stand-alone-guppy
https://hackmd.io/@GqOnlbqgSdKAMwgCUU_ljQ/r1YNeXnkO

# 运行：
guppy_basecaller -i demo/IVET/IVET_m6A -s demo/IVET/IVET_m6A_guppy --num_callers 40 --recursive --post_out --config rna_r9.4.1_70bps_hac.cfg
multi_to_single_fast5 -i demo/IVET/IVET_m6A_guppy -s demo/IVET/IVET_m6A_guppy_single --recursive #--cpu_threads_per_caller

###########################################
docker run -v $(pwd):/home/test chrishah/guppy:6.4.2-1   guppy_basecaller  -i /home/test/demo/IVET/IVET_m6A -s /home/test/demo/IVET/IVET_m6A_guppy --num_callers 40 --recursive --post_out --config rna_r9.4.1_70bps_hac.cfg

multi_to_single_fast5 -i demo/IVET/IVET_m6A_guppy -s demo/IVET/IVET_m6A_guppy_single --recursive #--cpu_threads_per_caller

docker run   -v $(pwd):/home/test edwardslab/tombo:latest  /opt/conda/bin/tombo  resquiggle --overwrite --basecall-group Basecall_1D_001 /home/test/demo/IVET/IVET_m6A_guppy_single  /home/test/demo/IVET_reference.fa --processes 40 --fit-global-scale --include-event-stdev

cat demo/IVET/IVET_m6A_guppy/pass/*.fastq >demo/IVET/IVET_m6A.fastq

#minimap2 -ax map-ont demo/IVET_reference.fa demo/IVET/IVET_m6A.fastq >demo/IVET/IVET_m6A.sam


docker run   -v $(pwd):/home/test edwardslab/tombo:latest  /opt/conda/bin/minimap2.py   -x map-ont  /home/test/demo/IVET_reference.fa  /home/test/demo/IVET/IVET_m6A.fastq -c /home/test/demo/IVET/IVET_m6A.sam

python scripts/extract_signal_from_fast5.py -p 40 --fast5 demo/IVET/IVET_m6A_guppy_single --reference demo/IVET_reference.fa --sam demo/IVET/IVET_m6A.sam --output demo/IVET/m6A.signal.tsv --clip 10
python scripts/extract_feature_from_signal.py  --signal_file demo/IVET/m6A.signal.tsv --clip 10 --output demo/IVET/m6A.feature.tsv --motif DRACH

????这里还没有测试完毕
python scripts/TandemMod.py --run_mode train \
     --new_model demo/model/m6A.demo.IVET.pkl \
     --train_data_mod demo/IVET/m6A.train.feature.tsv \
     --train_data_unmod demo/IVET/unmod.train.feature.tsv \
     --test_data_mod demo/IVET/m6A.test.feature.tsv \
     --test_data_unmod demo/IVET/unmod.test.feature.tsv \
     --epoch 100

python scripts/TandemMod.py --run_mode predict \
    --pretrained_model demo/model/m6A.demo.IVET.pkl \
    --feature_file demo/HEK293T/HEK293T.feature.tsv \
    --predict_result demo/HEK293T/HEK293T.prediction.tsv

python scripts/TandemMod.py --run_mode transfer \
    --pretrained_model demo/model/m6A.demo.IVET.pkl \
    --new_model demo/model/m7G.demo.ELIGOS.transfered_from_IVET_m6A.pkl \
    --train_data_mod demo/ELIGOS/m7G.train.feature.tsv \
    --train_data_unmod demo/ELIGOS/unmod.train.feature.tsv \
    --test_data_mod demo/ELIGOS/m7G.test.feature.tsv \
    --test_data_unmod demo/ELIGOS/unmod.test.feature.tsv \
     --epoch 100


