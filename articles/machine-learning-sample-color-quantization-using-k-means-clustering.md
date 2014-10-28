<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning Sample: Color quantization using K-Means clustering | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Пример машинного обучения Azure: квантование цвета с помощью кластеризации K-средних

*Пример эксперимента, связанный с этой моделью в ML Studio, можно найти в разделе **ЭКСПЕРИМЕНТЫ** на вкладке **ПРИМЕРЫ**. Имя эксперимента:*

    Sample Experiment - Color Based Image Compression using K-Means Clustering - Development

## Описание проблемы

[Квантование цвета][Квантование цвета] представляет собой процесс уменьшения количества различимых оттенков цвета на изображении, то есть, его сжатие. Как правило, задача заключается в том, чтобы максимально возможным образом сохранить воспроизведение цвета при сокращении количества оттенков либо для ограничения емкости памяти, либо для сжатия.

## Данные

В этом примере эксперимента мы предполагаем, что любое заданное 24-разрядное изображение RGB имеет 256 x 256 x 256 возможных цветов. Несомненно, что на основании этих значений интенсивности мы можем строить стандартные гистограммы цвета. Однако другой подход заключается в квантовании явным образом изображения и *уменьшении* количества цветов, например, до 16 или 64. Это создает заметно меньшее пространство, а также (в идеале) меньше шума и дисперсии. Для этого мы передали пиксельные данные (каждый пиксель в виде строки набора данных) в наш модуль кластеризации K-средних.

## Модель

Принцип создания модели показан на рис. ниже:

![Модель][Модель]

Мы выполнили кластеризацию K-средних, где K=10 — 500 в 5 различных ветвях. Сначала мы рассчитали кластеры, а затем объединили кластеризацию со средним значением их цветов пикселей (с помощью скрипта R)
.Наконец, мы связали каждый пиксель с объединенным цветом кластера и отправили новое изображение в формате CSV. Тем временем мы также рассчитали среднеквадратическое отклонение новых цветов пикселей с исходным изображением и показали их на графике R (с помощью модуля "Выполнение скрипта R").

## Результат

Мы проверили результат при другом количестве кластеров (цветов), как показано в модели эксперимента ниже. Хорошо видно, что более крупная кластеризация создает более качественные изображения с меньшим коэффициентом сжатия:

<table>
<tr><th>Оригинал</th>
<td><img alt="Original" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg"></td>
</tr>
<tr><th>K=10</th>
<td><img alt="K=10" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png"></td>
</tr>
<tr><th>K=20</th>
<td><img alt="K=20" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png"></td>
</tr>
<tr><th>K=50</th>
<td><img alt="K=50" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png"></td>
</tr>
<tr><th>K=100</th>
<td><img alt="K=100" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png"></td>
</tr>
<tr><th>K=500</th>
<td><img alt="K=500" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png"></td>
</tr>
</table>
Мы также измерили уровень точности с помощью среднеквадратического отклонения для цветов исходного изображения, которые можно увидеть из второго выходного порта модуля «Выполнение скрипта R»:

![Выход модуля «Выполнение скрипта R»][Выход модуля «Выполнение скрипта R»]

Хорошо видно, что чем больше кластеров цвета, тем лучше цвета соответствуют исходным изображениям (более высокое качество).

## Код для преобразования изображений в формат CSV и обратно

Чтобы подавать изображения в ML Studio, мы разработали простой код преобразователя, позволяющий преобразовывать файлы изображений в формат "csv", который может использовать пакет ML Studio, а также еще один код, преобразующий эти файлы обратно в изображение. Не стесняйтесь использовать следующий код. В будущем мы также планируем добавить модуль для считывания изображений.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Drawing;
    using System.Drawing.Imaging;
    using System.IO;
     
    namespace Text2Image
    {
        class Program
        {
            static void img2csv(string img_path)
            {
                FileInfo img_info = new FileInfo(img_path);
                string destination_file_directory = img_info.DirectoryName + "\\";
                string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
                string destination_file_path = destination_file_directory + destination_file_name + ".csv";
     
                // Read the image
                Bitmap img = new Bitmap(img_path);
     
                // Create the CSV File and write the header values
                System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
                file.WriteLine("X,Y,R,G,B");
     
                // Write the Pixel values
                for (int x = 0; x < img.Width; x++)
                    for (int y = 0; y < img.Height; y++)
                    {
                        string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
                        file.WriteLine(line);
                    }
     
                file.Close();
            }
     
            static void csv2img(string csv_path)
            {
                FileInfo csv_info = new FileInfo(csv_path);
                string destination_file_directory = csv_info.DirectoryName + "\\";
                string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
                string destination_file_path = destination_file_directory + destination_file_name + ".png";
                
                // Read all the lines in the CSV file
                string[] lines = System.IO.File.ReadAllLines(csv_path);
     
                // set a new bitmap image with the provided width and height in the header
                string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
                int img_width = Convert.ToInt32(wh[0])+1;
                int img_height = Convert.ToInt32(wh[1])+1;
     
                Bitmap bmp_img = new Bitmap(img_width, img_height);
     
                for (int i = 1; i < lines.Length ;i++ )
                {
                    string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
                    if (values.Length < 3)
                        continue;
     
                    int x = Convert.ToInt16(values[0]);
                    int y = Convert.ToInt32(values[1]);
                    int r = Convert.ToInt32(values[2]);
                    int g = Convert.ToInt32(values[3]);
                    int b = Convert.ToInt32(values[4]);
     
                    bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
                }
     
                bmp_img.Save(destination_file_path);
            }
     
            static void Main(string[] args)
            {
                string source_path = args[1];
                FileInfo source_info = new FileInfo(source_path);
     
                if (source_info.Extension == ".csv")
                    csv2img(source_path);
                else
                    img2csv(source_path);
            }
        }
    }

  [Квантование цвета]: http://en.wikipedia.org/wiki/Color_quantization "Квантование цвета"
  [Модель]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
  [Оригинал]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
  [K=10]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
  [K=20]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
  [K=50]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
  [K=100]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
  [K=500]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
  [Выход модуля «Выполнение скрипта R»]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png
