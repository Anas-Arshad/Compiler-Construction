using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;
using System.Text.RegularExpressions;
namespace filing
{

    class Compiler
    {
        
        
        static int line_no = 0;
        static int line_no_keywords=0;
        string[] KeywordsArray = { "If", "Else", "Act", "Until", "Loop", "Array", "Fresh", "Wh", "Dec", "Ribbon", "Ch" ,"Common","Personal","Secure"};
        string[] Punctuator_Array = { ",", "->", "~", "^", "<<", ">>" };
        char[] wordbreakers = {'-','+','*','/','>','<','!','@','$','%','^','&','(',')','{','}','[',']','=','~','^','\n' };


        public void Breaker()
        {
       

            //Console.WriteLine(KeywordsArray.Length);
            char[] delimiter = { ' ', '$', '\n', };
            
            StreamReader reader = new StreamReader(@"C:\Users\monis\Desktop\ReadMe.txt");
            StreamReader reader_for_keywords = new StreamReader(@"C:\Users\monis\Desktop\ReadMe.txt");
            while (!reader.EndOfStream)
            {
                string[] keywords = reader_for_keywords.ReadLine().Split(wordbreakers);//keywords
                line_no_keywords++;
                foreach (string word in keywords)
                {
                    CheckKeyword(word);
                }

                string[] words = reader.ReadLine().Split(delimiter);
                
                
                line_no++;
                

                foreach (string word in words)
                {

                    char[] temp_array = word.ToCharArray();

                    for (int i = 0; i < temp_array.Length - 1; i++)
                    {

                        //if ((temp_array[i] >= 65) && (temp_array[i] <= 90))//for keywords
                        //{
                        //  //  string temp = "";
                        //   // char ch = temp_array[i];
                        //   // while (!wordbreakers.Contains(ch) && i < temp_array.Length)
                        //    //{
                        //       // temp += temp_array[i].ToString();
                        //      //  ch = temp_array[i + 1];
                        //        //i++;
                        //    //?
                        //    //Console.WriteLine(temp);
                        //}

                         if (temp_array[i] == '#')//check variable
                        {

                            string temp = "";
                            char ch = temp_array[i];
                            while (!wordbreakers.Contains(ch) && i < temp_array.Length) //revolutionary line of code
                            //   while (ch != '/'&&ch != '=' && ch != '<' && ch != '!' && ch != '>' && ch!='+' && i < temp_array.Length)//<>(){}[],./!#$%^&*
                            {
                                temp += temp_array[i].ToString();
                                ch = temp_array[i + 1];
                                i++;

                            }

                            Console.WriteLine('(' + "Identifier" + ',' + temp + ',' + line_no + ")");//Remove when Inam sends RE


                        }
                        else
                        {
                            Console.WriteLine("Lexical Error at" + line_no);
                        }




                    }


                }
            }
        }






        public void CheckID(string ID)
        {
            Regex regex = new Regex(@"\d+");
           
            

        }

        public void CheckKeyword(string word)
        {
            for (int i = 0; i < KeywordsArray.Length; i++)
            {
                if (word == KeywordsArray[i])
                {
                    Console.WriteLine('('+ word+","+line_no_keywords+')');
                }
            }
            
        }



    }

    class Program
    {
        static void Main(string[] args)
        {

            Compiler c1 = new Compiler();
            c1.Breaker();



        }
    }
}
