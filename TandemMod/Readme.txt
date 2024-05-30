https://github.com/yulab2021/TandemMod/tree/master
#安装
sudo apt-get install zlib1g-dev #sudo yum install zlib-devel
mamba install pandas==1.3.5 patsy==0.5.3 packaging==23.1  progressbar33==2.4 python-dateutil==2.8.2  pytz==2023.3 statsmodels==0.10.0 tqdm==4.65.0 typing-extensions==4.6.3
pip install future==0.18.3
pip install mappy==2.26 ont-fast5-api==4.1.1 torch==1.9.1
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

