# About the Image Analyzer Extension
The Image Analyzer extension uses powerful image analytics provided by the Computer Vision API for Microsoft Cognitive Services to detect attributes in the images that you import for items and contact persons, so you can easily review and assign them. For items, attributes could be whether the item is a table or a car, and whether it is red or blue. For contact persons, attributes can be gender or age. For more information about our extension, see [Image Analyzer](https://docs.microsoft.com/en-us/dynamics365/business-central/ui-extensions-image-analyzer). To learn more about Computer Vision, see [Computer Vision API](https://go.microsoft.com/fwlink/?linkid=851476). 

## Example of Using the Image Analysis API
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
        for iii := 1 TO ImageAnalysisResult.TagCount do begin
            if ImageAnalysisResult.TagConfidence(iii)>BestTagConfidence then begin
                BestTag := ImageAnalysisResult.TagName(iii);
                BestTagConfidence := ImageAnalysisResult.TagConfidence(iii);
            end;
        end;
        item.SetRange(Description,BestTag);
        if item.FindFirst then
            page.Run(page::"Item Card",item);
    end;
}

```


