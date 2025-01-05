
# How to Scrape Google News into Excel

## Introduction

Scraping Google News data and exporting it to Excel can be highly valuable for tracking the latest headlines, analyzing trends, and staying informed. Using VBA (Visual Basic for Applications) in Excel, you can automate this process and extract data from Google News into a structured format, including the article title, source, publication time, and author.

This tutorial walks you through the process step-by-step, complete with a sample VBA code to make it easy for you to scrape Google News articles.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Google, Amazon, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## How the VBA Code Works

The VBA code provided below allows you to scrape Google News articles based on a user-provided query. It retrieves the following details for each article:

- **Title**
- **Source**
- **Time**
- **Author**
- **Link**

### Prerequisites

- **Microsoft Excel**: Ensure you have Excel installed with VBA enabled.
- **Basic Knowledge of Excel**: Familiarity with running macros in Excel.

---

## VBA Code for Scraping Google News

Here’s the complete VBA code for scraping Google News articles:

```vba
Sub GetNewsData()
    Dim query As String
    Dim url As String
    Dim xmlHttp As Object
    Dim htmlDoc As Object
    Dim articles As Object
    Dim article As Object
    Dim links As Collection
    Dim link As String
    Dim mytext As String
    Dim newsText As Collection
    Dim newsTextSplit As Variant
    Dim newsData As Collection
    Dim data As Collection

    ' Search Query
    query = InputBox("Please enter topic for news articles:", "User Input")
    If query = "" Then
        MsgBox "You didn't enter anything."
        Exit Sub
    End If

    ' Encode special characters in a text string
    query = EncodeSpecialCharacters(query)
    url = "https://news.google.com/search?q=" & query & "&hl=en-US&gl=US&ceid=US:en"

    ' Create a new XML HTTP request
    Set xmlHttp = CreateObject("MSXML2.XMLHTTP")
    xmlHttp.Open "GET", url, False
    xmlHttp.send

    ' Create a new HTML document
    Set htmlDoc = CreateObject("htmlfile")
    htmlDoc.body.innerHTML = xmlHttp.responseText

    ' Find all articles
    Set articles = htmlDoc.getElementsByTagName("article")

    ' Initialize collections
    Set links = New Collection
    Set newsData = New Collection

    ' Loop through each article
    For Each article In articles
        ' Get the link
        link = article.getElementsByTagName("a")(0).href
        link = Replace(link, "about:", "")
        link = Replace(link, "./articles/", "https://news.google.com/articles/")
        links.Add link

        ' Get the news text
        mytext = CleanTrim(article.innerText)

        ' Split the news text into lines
        newsTextSplit = Split(mytext, "\n")
        
        ' Add the data to the collection
        Set data = New Collection
        data.Add IIf(UBound(newsTextSplit) >= 2, Trim(newsTextSplit(2)), "Missing") ' Title
        data.Add Trim(newsTextSplit(0)) ' Source
        
        If UBound(newsTextSplit) >= 3 Then
            data.Add Trim(newsTextSplit(3)) ' Time
        Else
            data.Add "Missing"
        End If
            
        If UBound(newsTextSplit) >= 4 Then
            data.Add Trim(Split(newsTextSplit(4), "By ")(UBound(Split(newsTextSplit(4), "By ")))) ' Author
        Else
            data.Add "Missing"
        End If

        data.Add link ' Link
        newsData.Add data

    Next article

    Dim ws As Worksheet
    Dim mydata As Collection
    Dim i As Integer
    Dim j As Integer
    Dim response As VbMsgBoxResult

    ' Set the active worksheet
    Set ws = ActiveSheet

    ' Check if the sheet has data
    If WorksheetFunction.CountA(ws.UsedRange) > 0 Then
        ' Prompt the user to replace the data
        response = MsgBox("The active sheet contains data. Do you want to replace it?", vbQuestion + vbYesNo, "Replace Data")
        If response = vbYes Then
            ' Clear the data
            ws.Cells.ClearContents
        Else
           MsgBox "Data was not replaced. No New Data Added.", vbInformation, "Canceled"
           Exit Sub
        End If
    End If
    
    With ws
        .Cells(1, 1).Value = "Title"
        .Cells(1, 2).Value = "Source"
        .Cells(1, 3).Value = "Time"
        .Cells(1, 4).Value = "Author"
        .Cells(1, 5).Value = "Link"
    End With

    ' Get the data from the newsData collection
    Set mydata = newsData

    ' Write the data to the worksheet
    For i = 1 To mydata.Count
        For j = 1 To mydata(i).Count
            ws.Cells(i + 1, j).Value = mydata(i)(j)
        Next j
    Next i
End Sub

Function EncodeSpecialCharacters(text As String) As String
    Dim encodedText As String
    Dim char As String
    Dim i As Integer

    ' Encode the special characters
    encodedText = ""
    For i = 1 To Len(text)
        char = Mid(LCase(text), i, 1)
        Select Case char
            Case "&"
                encodedText = encodedText & "%26"
            Case "="
                encodedText = encodedText & "%3D"
            Case "+"
                encodedText = encodedText & "%2B"
            Case " "
                encodedText = encodedText & "%20"
            Case Else
                encodedText = encodedText & char
        End Select
    Next i

    EncodeSpecialCharacters = encodedText
End Function

Function CleanTrim(ByVal S As String) As String
    Dim X As Long, CodesToClean As Variant
    CodesToClean = Array(0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, _
                       21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 127, 129, 141, 143, 144, 157, 160)
    For X = LBound(CodesToClean) To UBound(CodesToClean)
        If InStr(S, Chr(CodesToClean(X))) Then S = Replace(S, Chr(CodesToClean(X)), "\n")
    Next
    CleanTrim = WorksheetFunction.Trim(S)
End Function
```

---

## Steps to Run the VBA Code

1. **Open a New Workbook**: Open Excel and create a new workbook.
2. **Access the VBA Editor**: Press `Alt + F11` or navigate to the **Developer** tab and click on **Visual Basic**.
3. **Insert a Module**:
   - In the VBA editor, go to `Insert > Module`.
   - Copy and paste the above VBA code into the module window.
4. **Run the Macro**:
   - Press `Alt + F8` in Excel.
   - Select the macro named `GetNewsData` and click **Run**.
5. **Enter Search Query**: When prompted, enter the topic for which you want to scrape Google News articles.

---

## Customization Tips

- **Hardcode the Query**: Replace `query = InputBox(...)` with `query = "Your Topic"` if you want a fixed search query.
- **Export Options**: The script can be modified to export data to CSV or other formats.

---

## Conclusion

This VBA script allows you to efficiently scrape Google News articles into Excel, providing structured and actionable insights for your chosen topics. For advanced and automated scraping needs, consider using [ScraperAPI](https://bit.ly/Scraperapi) to handle proxies, CAPTCHAs, and large-scale data requests.
