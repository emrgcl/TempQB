# TempQB8
- Lets learn ***git!***

> Note: Please note that this is Markdwown language!

```PowerShell
[CmdletBinding()]
Param(
    [ValidateScript({Test-path $_})]
    [string]$CounterListPath,
    [ValidateScript({Test-path $_})]
    [string]$ServerListPath,
    [Validaterange(10,300)]
    [int32]$SampleIntervalSeconds = 10,
    [ValidateRange(1,10)]
    [int32]$MaxSamples = 6
)
$StartTime= Get-Date
$ServerList = Get-content -Path $ServerListPath
$CounterList = Get-content -Path $CounterListPath
$ScriptBlock =  {
    $RawData = Get-Counter -Counter $using:CounterList -SampleInterval $using:SampleIntervalSeconds -MaxSamples $Using:MaxSamples
 
Foreach ($Entry in $RawData) {
 
    Foreach ($CounterSample in $Entry.CounterSamples) {
        if ($CounterSample.Path -match  '\\\\.+\\.+\\(?<CounterName>.+)$') {
 
            $CounterName =  $Matches['CounterName']
 
        } else {
            $CounterName = 'N/A'
        }
        [PSCustomObject]@{
            Path = $CounterSample.Path
            TimeStamp = $Entry.Timestamp
            InstanceName = $CounterSample.InstanceName
            CokkedValue = $CounterSample.CookedValue
            CounterName = $CounterName
        }
 
    }
 
}
}
$Session= New-PSSession -ComputerName $ServerList
$REsult = Invoke-command -Session $Session -ScriptBlock $ScriptBlock
$Result | ConvertTo-Json | Out-File -FilePath .\result.json -Append
Remove-PSSession $Session
 
Write-verbose "Script Duration: $(((Get-date) - $StartTime).TotalSeconds) Seconds"![image](https://user-images.githubusercontent.com/41307814/193574771-804726bf-972a-4e37-a9fe-1f620576817c.png)

```
