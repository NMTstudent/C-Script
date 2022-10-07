using System.Collections;

using System.Collections.Generic;

using UnityEngine;

using UnityEngine.Networking;

using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour

{

    public AudioClip goodSpeak;
    
    public AudioClip normalSpeak;
    
    public AudioClip badSpeak;
    
    private AudioSource selektAudio;
    
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    
    private bool statusStart = false;
    
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 
        & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
                
        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 
        & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
                
        if (dataSet["Mon_" + i.ToString()] >= 100 
        & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("http://sheets.googleapis.com/v4/spredsheets/1OPOa_CORvT2vRYKeUNGdec2AJUm0HebGexJIoqlQHwY/values/Лист1?key=AIzaSyA250T3MlgFzLdrFrL3VvKG42xuRKl1iKw");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
        Debug.Log(dataSet["Mon_1"]);
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selektAudio = GetComponent<AudioSource>();
        selektAudio.clip = goodSpeak;
        selektAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selektAudio = GetComponent<AudioSource>();
        selektAudio.clip = normalSpeak;
        selektAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selektAudio = GetComponent<AudioSource>();
        selektAudio.clip = badSpeak;
        selektAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }

}
