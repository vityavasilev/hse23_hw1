# hse23_hw1
### Васильев Виктор БПС214
# Обязательная часть задания
Копировал всю ночь файлы с пк на сервер. После этого обнаружил, что можно создать ссылки без передачи данных. Шаг создания ссылок пропущен, так как файлы уже есть на сервере (после выполнения ДЗ удалю)
## 1. seqtk
Создание файлов из исходных данных для последующей работы и анализа в fastqc и multiqc:
```bash
seqtk sample -s71 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s71 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s71 oilMP_S4_L001_R1_001.fastq 1500000 > matep1.fastq
seqtk sample -s71 oilMP_S4_L001_R2_001.fastq 1500000 > matep2.fastq
```
## 2. fastqc
Оценка чтений всех файлов sub и matep всех форматов
```bash
mkdir fastqc
ls sub* matep* | xargs -tI{} fastqc -o fastqc {}
```
## 3. multiqc
Отчет от multiqc
```bash
mkdir multiqc
multiqc -o multiqc fastqc
```
Обрезание чтений через платанус
```bash
platanus_trim sub*
platanus_internal_trim matep*
```
Оценка качества обрезанных чтений с помощью fastqc
```bash
mkdir fastqc_trimmed
ls sub* matep*| xargs -tI{} fastqc -o fastqc_trimmed {}
```
Создание отчета об отрезанных чтениях через multiqc
```bash
mkdir multiqc_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```
## 4. Контиги и платанус
```bash
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```
```bash
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> scaffold.log
```
```bash
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> gapclose.log
```

Очистка созданных вначале файлов...
```bash
rm sub* matep*
```
и отрезанных чтений.
```bash
rm sub*.trimmed matep*.int_trimmed
```
# Визуальные отчеты по обязательной части задания

Для изначальных чтений 
![](https://github.com/vityavasilev/hse23_hw1/blob/main/images/genstat_full.jpg)
![](https://github.com/vityavasilev/hse23_hw1/blob/main/images/psqc_full.jpg)

Для обрезанных чтений
![](https://github.com/vityavasilev/hse23_hw1/blob/main/images/genstat_trim_idk.jpg)
![](https://github.com/vityavasilev/hse23_hw1/blob/main/images/psqc_trim.jpg)

Вероятно, произошла ошибка, из-за которой обработались данные только одного файла, но по коду не могу определить, где она была допущена. Присутствуют данные файла matep1.
