<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Пример машинного обучения: квантование цвета с помощью кластеризации K-средних | Azure" description="Пример эксперимента с Машинным обучением Azure, который оценивает применение различных значений кластеризации методом К-средних для квантования цветного изображения." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Пример машинного обучения Azure: квантование цвета с помощью кластеризации K-средних

>[AZURE.NOTE]
>[Пример эксперимента] и [образец набора данных], связанные с этой моделью, доступны в ML Studio. См. дополнительные сведения ниже.
[Пример эксперимента]: #sample-experiment
[Образец набора данных]: #sample-dataset


##Описание проблемы

[Квантование цвета](http://en.wikipedia.org/wiki/Color_quantization "Color quantization") - это процесс уменьшения количества различимых оттенков цвета на изображении, то есть его сжатие. Как правило, задача заключается в том, чтобы максимально сохранить воспроизведение цвета при сокращении количества оттенков для ограничения памяти либо сжатия. 

##Данные

В этом примере эксперимента мы предполагаем, что любое заданное 24-разрядное изображение RGB имеет 256x256x256 возможных цветов. Несомненно, на основании этих значений интенсивности мы можем строить стандартные гистограммы цвета. Однако другой подход заключается в квантовании явным образом изображения и *уменьшении* количества цветов, например, до 16 или 64. Это создает заметно меньшее пространство, а также (в идеале) меньше шума и дисперсии. Для этого мы передали пиксельные данные (каждый пиксель в виде строки набора данных) в наш модуль кластеризации K-средних. 

##Модель

Принцип создания модели показан на рис. ниже:

![Model][image1]

Мы выполнили кластеризацию K-средних, где K = 10-500 в 5 различных ветвях. Сначала мы рассчитали кластеры, а затем свели кластеризацию к среднему значению их цветов пикселей (с помощью скрипта R). 
Затем мы связали каждый пиксель со сводным цветом кластера и выпустили новое изображение в формате CSV. Мы также рассчитали среднеквадратическое отклонение новых цветов пикселей с исходным изображением и показали их на графике R (с помощью модуля "Выполнение скрипта R"). 

##Результаты

Мы проверили результат на разном количестве кластеров (цветов), как показано в модели эксперимента ниже. Ниже видно, что более крупная кластеризация создает более качественные изображения с меньшим коэффициентом сжатия:

||
------------ | ---------
**Исходное** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


Мы также измерили уровень точности с помощью среднеквадратического отклонения для цветов исходного изображения, которые можно увидеть из второго выходного порта модуля "Выполнение скрипта R":

![Output of Execute R Script module][image3]

Хорошо видно, что чем больше кластеров цвета, тем лучше цвета соответствуют исходным изображениям (более высокое качество). 

##Код для преобразования изображений в формат CSV и обратно

Чтобы подавать изображения в ML Studio, мы разработали простой код преобразователя, позволяющий преобразовывать файлы изображений в формат CSV, который может использовать ML Studio, а также еще один код, преобразующий эти файлы обратно в изображение. Не стесняйтесь использовать следующий код. В будущем мы также планируем добавить модуль для считывания изображений. 

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


## Пример эксперимента

Следующий пример эксперимента, связанный с этой моделью, доступен в ML Studio в разделе **ЭКСПЕРИМЕНТЫ** на вкладке **ПРИМЕРЫ**.

> **Пример эксперимента - Сжатие изображения на основе цвета с помощью кластеризации K-средних - Разработка**


## Образец набора данных

Следующий образец набора данных, используемый для этого эксперимента, доступен в ML Studio в палитре модулей в разделе **Сохраненные наборы данных**.

###Изображение RGB Билла Гейтса
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

