using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;
using System.Text.RegularExpressions;

namespace Nascent
{

    class Compiler
    {


        static public int line_no = 0;
        // static int line_no_keywords = 0;
        string[] KeywordsArray = { "If", "Else", "Act", "Until", "Loop", "Array", "Fresh", "Wh", "Dec", "Ribbon", "Ch", "Common", "Personal", "Secure" };
        string[] Punctuator_Array = { ",", "->", "~", "^", "<<", ">>" };
        char[] wordbreakers = { '-', '+', '*', '/', '>', '<', '!', '@', '$', '^', '&', '(', ')', '{', '}', '[', ']', '=', '~', '^', '\n', ' ' };



        public void Breaker()
        {


           
            //Console.WriteLine(KeywordsArray.Length);
            char[] delimiter = { '\n', ' ' };

            StreamReader reader = new StreamReader(@"C:\Users\ana\Desktop\ReadMe.txt");
            StreamReader reader_for_keywords = new StreamReader(@"C:\Users\ana\Desktop\ReadMe.txt");
            while (!reader.EndOfStream)
            {
                //string[] keywords = reader_for_keywords.ReadLine().Split(wordbreakers);//keywords
                //line_no_keywords++;
                //        foreach (string word in keywords)
                //      {
                //        CheckKeyword(word);


                //  }



                string[] words = reader.ReadLine().Split(delimiter);


                line_no++;


                foreach (string word in words)
                {

                    //char[] temp_array = word.ToCharArray();
                    //List<char> t = temp_array.ToList<char>();
                    List<char> temp_array = word.ToList<char>();

                    for (int i = 0; i < temp_array.Count; i++)
                    {

                        if (temp_array[i] >= 65 && temp_array[i] <= 90)//kEYWORDS
                        {
                            bool flag = false;
                            string temp = "";
                            char ch = temp_array[i];
                            int a = i;

                            while (!wordbreakers.Contains(ch) && a < temp_array.Count) //revolutionary line of code
                            {
                                temp += temp_array[a].ToString();
                                if (a + 1 < temp_array.Count)

                                    ch = temp_array[a + 1];

                                a++;

                            }
                            i = a - 1;


                            CheckKeyword(temp);

                        }
                        else
                            if (temp_array[i] >= 97 && temp_array[i] <= 127)//kEYWORDS
                            {
                                bool flag = false;
                                string temp = "";
                                char ch = temp_array[i];
                                int a = i;

                                while (!wordbreakers.Contains(ch) && a < temp_array.Count) //revolutionary line of code
                                {
                                    temp += temp_array[a].ToString();
                                    if (a + 1 < temp_array.Count)

                                        ch = temp_array[a + 1];

                                    a++;

                                }
                                i = a - 1;


                                CheckID(temp);

                            }

                        else if (temp_array[i] == '#' && i < temp_array.Count)//check variable
                        {
                            bool flag = false;
                            string temp = "";
                            char ch = temp_array[i];
                            int a = i;
                            while (!wordbreakers.Contains(ch) && a < temp_array.Count) //revolutionary line of code
                            //   while (ch != '/'&&ch != '=' && ch != '<' && ch != '!' && ch != '>' && ch!='+' && i < temp_array.Length)//<>(){}[],./!#$%^&*
                            {


                                temp += temp_array[a].ToString();
                                if (a + 1 < temp_array.Count)
                                {
                                    ch = temp_array[a + 1];

                                }
                                if (ch == '$')
                                {
                                    flag = true;
                                }
                                a++;

                            }
                            i = a - 1;
                            CheckID(temp);
                            if (flag == true)
                            {
                                Console.WriteLine("(Terminator , $ , " + line_no);
                            }

                        }





                         //region for operators

                        #region


                        else if (temp_array[i] == '+' || temp_array[i] == '-' && i < temp_array.Count)
                        {
                            char ch = temp_array[i];
                            if ((i + 1) < temp_array.Count && temp_array[i + 1] == ch)//i+1 exsist?
                            {
                                Console.WriteLine("(" + "INCDEC" + ',' + ch + "" + ch + "," + line_no + ')');//INCDEC
                                temp_array.RemoveAt(i + 1);

                            }
                            else if ((i + 1) < temp_array.Count && temp_array[i + 1] == '=')//(i+1) must exsist 
                            {
                                Console.WriteLine("(" + "AOP" + ',' + ch + "" + temp_array[i + 1] + "," + line_no + ')');
                                temp_array.RemoveAt(i + 1);

                            }
                            else if ((i + 1) < temp_array.Count && temp_array[i] == '-' && temp_array[i + 1] == '>')// ->
                            {
                                Console.WriteLine("(" + "RefOp" + ',' + ch + "" + temp_array[i + 1] + "," + line_no + ')');//->
                                temp_array.RemoveAt(i + 1);

                            }
                            else

                                Console.WriteLine("(" + "ARP" + ',' + ch + "," + line_no + ')');//INCDEC
                        }

                        else if ((temp_array[i] == '*' || temp_array[i] == '/' || temp_array[i] == '%')
                            && (i < temp_array.Count))// *  /
                        {

                            char ch = temp_array[i];

                            if ((i + 1) < temp_array.Count && temp_array[i + 1] == '=')//(i+1) must exsist
                            {
                                Console.WriteLine("(" + "AOP" + ',' + ch + "" + temp_array[i + 1] + ',' + line_no + ')');
                            }
                            else
                            {
                                Console.WriteLine("(" + "MDM" + ',' + ch + ',' + line_no + ')');
                            }
                        }

                        else if ((temp_array[i] == '!') && (i < temp_array.Count))     // *  /
                        {

                            char ch = temp_array[i];

                            if ((i + 1) < temp_array.Count && temp_array[i + 1] == '=')//(i+1) must exsist
                            {
                                Console.WriteLine("(" + "ROP" + ',' + ch + "" + temp_array[i + 1] + ',' + line_no + ')');
                            }
                            else
                            {
                                Console.WriteLine("(" + "NOT" + ',' + ch + ',' + line_no + ')');
                            }
                        }

                        else if (temp_array[i] == '<' && i < temp_array.Count)//<< braces and <
                        {
                            char ch = temp_array[i];
                            if ((i + 1) < temp_array.Count && temp_array[i + 1] == ch)//i+1 exsist?
                            {
                                Console.WriteLine("(" + "Braces Open" + ',' + ch + "" + ch + "," + line_no + ')');//INCDEC
                                temp_array.RemoveAt(i + 1);
                            }
                            else if ((i + 1) < temp_array.Count && temp_array[i + 1] == '=')//(i+1)<temp_array.Length&& 
                            {
                                Console.WriteLine("(" + "ROP" + ',' + ch + "" + temp_array[i + 1] + "," + line_no + ')');
                                temp_array.RemoveAt(i + 1);
                            }
                            else
                                Console.WriteLine("(" + "ROP" + ',' + ch + "," + line_no + ')');//INCDEC
                        }
                        else if (temp_array[i] == '>' && i < temp_array.Count)//>> braces >
                        {
                            char ch = temp_array[i];
                            if ((i + 1) < temp_array.Count && temp_array[i + 1] == ch)//i+1 exsist?
                            {
                                Console.WriteLine("(" + "Braces Closed" + ',' + ch + "" + ch + "," + line_no + ')');//INCDEC
                                temp_array.RemoveAt(i + 1);
                            }
                            else if ((i + 1) < temp_array.Count && temp_array[i + 1] == '=')//(i+1)<temp_array.Length&& 
                            {
                                Console.WriteLine("(" + "ROP" + ',' + ch + "" + temp_array[i + 1] + "," + line_no + ')');
                                temp_array.RemoveAt(i + 1);
                            }
                            else
                                Console.WriteLine("(" + "ROP" + ',' + ch + "," + line_no + ')');//INCDEC
                        }


                        else if (temp_array[i] == '=' && i < temp_array.Count)// ==
                        {
                            char ch = temp_array[i];
                            if ((i + 1) < temp_array.Count && temp_array[i + 1] == ch)//i+1 exsist?
                            {

                                Console.WriteLine("(" + "ROP" + ',' + ch + "" + ch + "," + line_no + ')');//INCDEC
                                temp_array.RemoveAt(i + 1);
                            }
                            else
                            {
                                Console.WriteLine("(" + "AOP" + ',' + ch + "," + line_no + ')');//INCDEC
                            }
                        }




                        #endregion



                        //region for ribbon

                        #region

                        else if (temp_array[i] == '\"')   // Ribbon
                        {
                            string temp = "";
                            int j;
                            bool flag = false;
                            int a = i;

                            if (flag == false)
                            {
                                for (j = i; j < temp_array.Count; j++)
                                {

                                    if (j < temp_array.Count && temp_array[j] == '\"')
                                    {
                                        if ((j - 1) >= 0 && temp_array[j - 1] != '\\')
                                        {
                                            temp += '\"';
                                            a++;
                                            flag = true;
                                            break;
                                        }
                                        else
                                        {

                                            temp += temp_array[j];


                                        }

                                    }

                                    else
                                    {
                                        temp += temp_array[j];

                                    }

                                    a++;
                                }
                            }
                            i = a - 1;
                            flag = true;
                            CheckRibbon(temp);

                        }





                        #endregion

                        //regionforcharachter

                        #region


                        else if (temp_array[i] == '\'' && i < temp_array.Count)//character
                        {

                             for (int z = 0; z < temp_array.Count; z++)
                            {
                             //   Console.Write(temp_array[z]);
                            }

                            string temp = "\'";



                            if (i + 1 < temp_array.Count && temp_array[i + 1] == '\\')
                            {

                                
                                temp += temp_array[i + 1]; //tostring
                                

                                if (i + 2 < temp_array.Count)
                                {
                                    temp += temp_array[i + 2];
                                    

                                }
                                if (i + 3 < temp_array.Count&&temp_array[i+3]=='\'')
                                {
                                    temp += temp_array[i + 3];
                                    i += 3;
                                    
                                 }

                            }
                            else if (i + 1 < temp_array.Count && temp_array[i + 1] != '\\')
                            {
                                temp += temp_array[i + 1];
                                if (i + 2 < temp_array.Count)
                                {
                                    temp += temp_array[i + 2];
                                    i += 2;// verify it 
                                }
                            }
                            //Console.WriteLine(temp);
                            CheckChar(temp);
                            
                              }



                        #endregion





                        //DATA TYPES 

                        else if (temp_array[i] >= 48 && temp_array[i] <= 57)
                        {
                            string temp = "";
                            int a = i;


                            char ch = temp_array[a];
                            while (!wordbreakers.Contains(ch) && (a < temp_array.Count)) //revolutionary line of code
                            {
                                temp += temp_array[a].ToString();
                                if (a + 1 < temp_array.Count)
                                    ch = temp_array[a + 1];

                                a++;


                            }

                            checkDAataType(temp);
                            i = a - 1;
                        }
                        else if (temp_array[i] == ',')
                            Console.WriteLine("(,  ," + line_no + ')');//comma
                        else if (temp_array[i] == '(')
                            Console.WriteLine("( ( , " + line_no + " )");//(
                        else if (temp_array[i] == ')')
                            Console.WriteLine("( ) , " + line_no + " )");//(
                        else if (temp_array[i] == ':')
                            Console.WriteLine("( : , " + line_no + " )");//(
                        else if (temp_array[i] == '$')
                        {
                            Console.WriteLine("( Terminator , $ ," + line_no + " )");
                        }


                        else if (temp_array[i] == '@')
                        {
                            goto Continue;
                        }
                        else
                        {
                            Console.WriteLine("Lexical error at line no " + line_no + " character = " + temp_array[i]);
                        }





                    }

                Continue:
                    continue;
                }
            }
        }



        //region for REs

        #region


        public void checkDAataType(string number)
        {
            Regex regex_for_wh = new Regex(@"^([+-]?\d+)$");          // whole
            Regex regex_for_dec = new Regex(@"^([+-]?\d+)?\.\d+(e[+-]?\d+)?$");       //dec
            Match match_for_wh = regex_for_wh.Match(number);
            Match match_for_dec = regex_for_dec.Match(number);
            if (match_for_wh.Success)
                Console.WriteLine("(" + "WH_const," + match_for_wh.Value + "," + line_no + ")");
            else if (match_for_dec.Success)
                Console.WriteLine("(" + "Dec_const," + match_for_dec.Value + "," + line_no + ")");

            else

                Console.WriteLine("Lexical Error at line no " + line_no);

        }

        public void CheckID(string ID)
        {
            Regex regex = new Regex(@"^#(?!_)\w+$");
            //Regex regex = new Regex(@"^([a-z]+)([a-zA-Z0-9]*)$");     //identifier
            Match match = regex.Match(ID);
            if (match.Success)
                Console.WriteLine("(" + "Identifier , " + match.Value + " ," + line_no + " )");

            else
                Console.WriteLine("Lexical Error at line no" + line_no);


        }


        public void CheckChar(string word)
        {

            //word = "\"" + word + "\"";
            Console.WriteLine(word);
            Regex regex1 = new Regex(@"^'(\w)'$");
            Regex regex2 = new Regex(@"^'(\s)'$");
            Match match1 = regex1.Match(word);
            Match match2 = regex2.Match(word);

            
            if (match1.Success)
            {
                Console.WriteLine("Valid");
                
            }

            if (match2.Success)
            {
                Console.WriteLine("Valid");
              
            }

            else if(!match1.Success&&!match2.Success)
            {
                Console.WriteLine("invalid");
            }
            
            
            
        }



        public void CheckRibbon(string word) //string
        {
            Regex regex1 = new Regex(@"^""(.)*""$");

            Match match1 = regex1.Match(word);

            if (match1.Success)
            {

                Console.WriteLine('(' + "Ribbon ," + word + "," + line_no + ')');
            }
            else
            {

                Console.WriteLine("Lexical error  character=\"  at line no " + line_no);
            }

        }

        public void CheckKeyword(string word)
        {
            bool flag_for_keyword = false;
            for (int i = 0; i < KeywordsArray.Length; i++)
            {
                if (word == KeywordsArray[i])
                {

                    flag_for_keyword = true;
                }
            }
            if (flag_for_keyword == true)
            {
                Console.WriteLine("( Keyword , " + word + " , " + line_no + ')');

            }
            else
                Console.WriteLine("Lexical error at line no " + line_no);

        }
        #endregion

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
