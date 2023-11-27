NPIP中文使用文档

NPIP是由中国疾病预防控制中心传染病预防控制所生物信息室研发并维护的一款基于三代测序Nanopore数据的病原菌筛查流程。该流程适用于临床样本，如粪便、脑脊液、肺泡灌洗液等宏基因组测序结果的疑似病原体的筛查。当前版本为Version1.1。
注：该工具可用于现有实验室检测方法的辅助，不可直接用于临床诊断。

一、NPIP安装
1、下载
wget npip.tar
2、解压缩
tar xzvf npip.tar
3、运行镜像
docker run -it -v <Local File Directory>:/Run npip:1.1
注：NPIP程序的安装依赖于Docker软件，建议提前于Linux系统中安装。Docker安装方法，可参考：https://mp.weixin.qq.com/s/9F6lS7fN8nakoReR8ufblQ 

二、NPIP运行
注意：NPIP的运行需在的相应Docker镜像环境下。
1、原始测序序列质控（可跳过）
Npip -qc -I <Input File>

2、列出目前已有的菌种列表
Npip -list ：列出当前16S和Genome数据库中所有菌种
Npip -list -16S：列出当前16S数据库中所有菌种
Npip -list -genome：列出当前Genome数据库中所有菌种
Npip -list <Name>：列出当前16S和Genome数据库中指定菌种，如NPIP -list Yer
3、对单个Fastq文件进行病原菌鉴定
(1)基于16S进行病原菌鉴定：
npip -16s -I <Input File>
(2)基于Genome数据库进行病原菌鉴定
npip -G -I <Input File>
4、先进行文库拆分demultiplex，再对各个样本分别进行病原菌鉴定
（1）文库拆分后，对各样本分别基于16S进行病原菌鉴定：
npip -D -16s -I <Input File>
（2）文库拆分后，对各样本分别基于Genome进行病原菌鉴定：
npip -D -G -I <Input File>
（3）仅进行文库拆分
npip -D -I <Input file>
5、针对某特定病原菌进行筛查
npip -T <Target Genome> -I <Inputfile> 
注：<Target Genome>为指定病原菌的基因组序列（Fasta格式）

6、帮助
npip -help


三、输入文件
输入文件：目前只支持Fastq格式。
四、输出文件
(1)基于16S数据库的病原菌鉴定结果 (-16S参数下)
*.report 结果存储于Outputdir/Report文件夹下。
Outputdir默认为当前路径下的NanoporeOut文件夹。
输出文件可用文本编辑器、excel打开。
报告分两个部分：
A、第一个部分，列出属水平上检出结果，以“DRM information on Genus level”开头。
每一行代表一个检出的疑似菌属
第一列：筛查出菌属名(Genus Name),
第二列：比对序列数(Read Num),
第三列：相对丰度(Percentage%),
第四列：可信度 (Identity value),
第五列：有可能混淆的菌种(Candidate Errors)
B、第二个部分，列出种水平上检出结果，以“DRM information on Species level”开头。
每一行代表一个检出的疑似菌种
第一列：筛查出菌种名(Species Name),
第二列：比对序列数(Read Num),
第三列：相对丰度(Percentage%),
第四列：可信度 (Identity value),
第五列：有可能混淆的菌种(Candidate Errors)

(2)基于基因组数据库的病原菌鉴定结果(-G参数下)
*.report 结果存储于Outputdir/Report文件夹下。
Outputdir默认为当前路径下的NanoporeOut文件夹。
输出文件可用文本编辑器、excel打开。
输出文件各列间用“,”分隔，每一行代表一个菌种。
第一列：筛查出菌种名(Species_name),
第二列：比对的Read数 (Read Num)，
第三列：目标菌种基因组大小 (Genome Size)，
第四例：比对长度 (Match Size)
第五列：覆盖度(Coverage%)
第六列：覆盖深度 (Depth)
第七列：该属特异性比对序列数 (Uniq Reads for Genus)
第八列：该种特异性序列比对数 (Uniq Reads for Species)
第九列：特异性序列覆盖度 (Coverage% for Uniq Reads)
(3)针对特定病原菌基因组序列的筛查结果(-T/-target参数下)
结果存储于Outputdir/Target文件夹下。
Outputdir默认为当前路径下的NanoporeOut文件夹。
输出文件可用文本编辑器、excel打开。
A、*.stat.result
统计结果。
B、*.bowtie.report
输出文件各列间用“,”分隔，每一行代表目标菌种的一个基因片段（如为基因组完成图，则仅1行；如目标基因组为草图，则每一个contig为1行）。
Gene ID,Target Genome Length,Match Read Num,Align Length,Coverage%,Depth,Target Description
(4)测序质控结果(-qc参数下)
质控报告存储于Outputdir/fastqc文件夹下。
Outputdir默认为当前路径下的NanoporeOut文件夹。
输出文件为html格式，可通过网页浏览器打开。
(5)文库拆分结果(-D参数下)
质控报告存储于Outputdir/barcode文件夹下。
Outputdir默认为当前路径下的NanoporeOut文件夹。
输出文件为fastq格式。
(6)列表或数据库查询结果(-list参数下)
当前页面直接输出当前版本数据库内的所有菌种或符合关键词的菌种。
