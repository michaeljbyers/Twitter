# Twitter
ASP.NET code when displaying Twitter stream on page

Public Function fixTwitterDate(ByVal dt As DateTime) As String
        'Return dateadded.ToString("dddd") & ", " & dateadded.ToString("dd MMMM yyyy")
        'Return dateadded.ToString("dd MMMM yyyy")

        If dt > DateTime.Now Then
            Return "sometime from now"
        End If

        Dim span As TimeSpan = DateTime.Now - dt

        If (span.Days > 365) Then
            Dim years As Integer = (span.Days / 365)
            If span.Days Mod 365 <> 0 Then
                years += 1
                Return String.Format("{0} {1} ago", years, If(years = 1, "year", "years"))
            End If
        End If

        If span.Days > 30 Then
            Dim months As Integer = (span.Days / 30)
            If span.Days Mod 31 <> 0 Then
                months += 1
            End If
            Return String.Format("{0} {1} ago", months, If(months = 1, "month", "months"))
        End If

        If span.Days > 0 Then
            Return String.Format("{0} {1} ago", span.Days, If(span.Days = 1, "day", "days"))
        End If

        If span.Hours > 0 Then
            Return String.Format("{0} {1} ago", span.Hours, If(span.Hours = 1, "hour", "hours"))
        End If

        If span.Minutes > 0 Then
            Return String.Format("{0} {1} ago", span.Minutes, If(span.Minutes = 1, "minute", "minutes"))
        End If

        If span.Seconds > 5 Then
            Return String.Format("{0} seconds ago", span.Seconds)
        End If

        If span.Seconds <= 5 Then
            Return "just now"
        End If

        Return String.Empty


    End Function

    Protected Function MakeLink(ByVal txt As String) As String
        Dim regx As New Regex("http(s)?://([\w+?\.\w+])+([a-zA-Z0-9\~\!\@\#\$\%\^\&amp;\*\(\)_\-\=\+\\\/\?\.\:\;\'\,]*)?", RegexOptions.IgnoreCase)

        Dim mactches As MatchCollection = regx.Matches(txt)

        For Each match As Match In mactches
            txt = txt.Replace(match.Value, "<a href=""" & match.Value & """>" & match.Value & "</a>")
        Next

        Return txt
    End Function

    Protected Function MarkHashtag(ByVal txt As String) As String
        Dim regx As New Regex("(?<=\s|^)#(\w*[A-Za-z_]+\w*)", RegexOptions.IgnoreCase)

        Dim matches As MatchCollection = regx.Matches(txt)

        For Each match As Match In matches
            txt = txt.Replace(match.Value, "<a href=""https://twitter.com/search?q=" & match.Value & """>" & match.Value & "</a>")
        Next

        Return txt
    End Function

    Protected Function MarkUser(ByVal txt As String) As String
        Dim regx As New Regex("(?<=\s|^)@(\w*[A-Za-z_]+\w*)", RegexOptions.IgnoreCase)

        Dim matches As MatchCollection = regx.Matches(txt)

        For Each match As Match In matches
            Dim submatch As String = match.Value.Substring(1, match.Value.Length - 1)
            txt = txt.Replace(match.Value, "<a href=""https://twitter.com/" & submatch & """>" & match.Value & "</a>")
        Next

        Return txt
    End Function
