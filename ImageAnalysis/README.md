# About the Image Analyzer Extension
The Image Analyzer extension uses powerful image analytics provided by the Computer Vision API for Microsoft Cognitive Services to detect attributes in the images that you import for items and contact persons, so you can easily review and assign them. For items, attributes could be whether the item is a table or a car, and whether it is red or blue. For contact persons, attributes can be gender or age. For more information about our extension, see [Image Analyzer](https://docs.microsoft.com/en-us/dynamics365/business-central/ui-extensions-image-analyzer). To learn more about Computer Vision, see [Computer Vision API](https://go.microsoft.com/fwlink/?linkid=851476). 

## Additional Requirements
In addition to the requirements listed in the [Readme](readme.md) file for the ALAppExtensions repository, if you really want to get hands-on with this code consider using your own model. It's fairly easy to do, but you will need an Azure subscription. Here are the steps.

### To create your own model, and then use it in Business Central
1. In a browser, go to [ https://www.customvision.ai/](https://www.customvision.ai/).
2. Create a new project. Under **Domains**, choose **General**.
3. Upload and tag images. 
  
    > [!Note]
    > For each tag you'll need around 38 images. However, it's a good idea to have more than that number of images so that you have some to test the model's accuracy.
  
4. Train the model. <!--Find a link to information about training a model -->
5. Test the model by choosing the **Quick Test** action.
6. On the **Performance** tab, copy the API URI.
7. Choose the **Setting** action to get the API key.
8. Choose the ![Search for Page or Report](media/ui-search/search_small.png "Search for Page or Report icon") icon, enter **Image Analysis Setup**, and then choose the related link.
9. Fill in the **API URI** and **API Key** fields.
10. In the **Limit Type** field, enter a period of time.
<!-- 11. In the **Limit Value** field, FIND OUT WHAT THIS IS!!!  -->

## Code Example
This is just one way to do things, but here's an example of the AL code in action. The syntax is similar to C/AL.

```
codeunit <_number_> "<_codeunit name_>" 
{
    var
        ImageAnalysisManagement : Codeunit "Image Analysis Management";
        ImageAnalysisResult : Codeunit "Image Analysis Result";

    procedure DemoCust1()
    var
        iii : Integer;
        BestTag : Text;
        BestTagConfidence : Decimal;
        item : Record Item;
        FileMng : Codeunit "File Management";
        TempBlob : Record TempBlob temporary;
    begin
        FileMng.BLOBImportWithFilter(TempBlob,'Choose file','','Images only|*.jpg','*.jpg');
        ImageAnalysisManagement.Initialize();
        ImageAnalysisManagement.SetBlob(TempBlob);
        ImageAnalysisManagement.AnalyzeTags(ImageAnalysisResult);
        FOR iii := 1 TO ImageAnalysisResult.TagCount DO BEGIN
            if ImageAnalysisResult.TagConfidence(iii)>BestTagConfidence then begin
                BestTag := ImageAnalysisResult.TagName(iii);
                BestTagConfidence := ImageAnalysisResult.TagConfidence(iii);
            end;
        END;
        item.SetRange(Description,BestTag);
        if item.FindFirst then
            page.Run(page::"Item Card",item);
    end;
}

```


