# Java-program-that-searches-for-records
java code that searches for records in a text file
package javaapplication2;

import java.io.BufferedWriter;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
import java.util.List;
import java.util.Scanner;

public class Q2_final {

    static class record {

        private String fullName = "";
        private String email = "";
        private String organization = "";
        private String country = "";

        public void initialize(String n, String e, String o, String c) {
            this.fullName = n;
            this.email = e;
            this.organization = o;
            this.country = c;
        }

        public void initialize(String str) {
//			System.out.println("Processing "+str);
            String n = "";
            String e = "";
            String o = "";
            String c = "";
            String elements[] = str.split(":");
            if (elements.length < 4) {
                System.out.println("Error, string is short " + str + " length=" + elements.length);
            }
            if (elements.length > 0) {
                n = elements[0].trim();
            }
            if (elements.length > 1) {
                e = elements[1].trim();
            }
            if (elements.length > 2) {
                o = elements[2].trim();
            }
            if (elements.length > 3) {
                c = elements[3].trim();
            }
            initialize(n, e, o, c);
        }

        public String getAll() {
            return fullName + ": " + email + ": " + organization + ": " + country;
        }

        public boolean search(String field, String value) {
            String str = "";
            if (field.compareTo("name") == 0) {
                str = fullName;
            }
            if (field.compareTo("email") == 0) {
                str = email;
            }
            if (field.compareTo("organization") == 0) {
                str = organization;
            }
            if (field.compareTo("country") == 0) {
                str = country;
            }
            if (!str.isEmpty()) {
                if (str.compareTo(value) == 0) {
                    return true;
                } else {
                    return false;
                }
            } else {
                return false;
            }
        }

        public String getName() {
            return fullName;
        }

        public String getEmail() {
            return email;
        }

        public String getOrganization() {
            return organization;
        }

        public String getCountry() {
            return country;
        }

        public int getMaxChar() {
            String s = this.getAll();
            int max = 0;
            for (int i = 0; i < s.length(); i++) {
                if (s.charAt(i) > max) {
                    max = s.charAt(i);
                }
            }
            return max;
        }
    }

    static class index {

        private String value;
        private int hash;
        private int number;
        private index nextIndex;

        /**
         * Returns node's value
         *
         * @return integer
         */
        public String getValue() {
            return value;
        }

        /**
         * Returns node's hash
         *
         * @return hash
         */
        public int getHash() {
            return hash;
        }

        /**
         * Returns node's number
         *
         * @return number
         */
        public int getNumber() {
            return number;
        }

        /**
         * Set node value
         *
         * @param v integer to set
         */
        public void setIndex(String s, int h, int n) {
            this.value = s;
            this.hash = h;
            this.number = n;
        }

        public index getNext() {
            return nextIndex;
        }

        public void setNext(index ref) {
            this.nextIndex = ref;
        }

    }

    public static int numberLines(String filename) {
        File myfile = new File(filename);
        try {
            int n;
            try (Scanner myscan = new Scanner(myfile)) {
                System.out.println("Counting number of lines in file " + myfile.getAbsolutePath());
                n = 0;
                while (myscan.hasNextLine()) {
                    n++;
                    myscan.nextLine();
                }
            }
            return n;
        } catch (FileNotFoundException e) {
            System.out.println("Error opening file " + myfile.getAbsolutePath());
            return -1;
        }
    }

    public static int readFile(String filename, record[] array) {
        File myfile = new File(filename);
        int i = 0;
        String temp;
        try {
            Scanner myscan = new Scanner(myfile, "utf-8");
            System.out.println("File " + myfile.getAbsolutePath() + " is opened");
            for (i = 0; myscan.hasNextLine(); i++) {
                if (i == array.length) {
                    System.out.println("String array is not big enough, stopping reading");
                    break;
                }
                temp = myscan.nextLine().trim();
                array[i].initialize(temp);
            }
            myscan.close();
            System.out.println("File " + myfile.getAbsolutePath() + " closed");

        } catch (FileNotFoundException e) {
            System.out.println("Error opening file " + myfile.getAbsolutePath());
        }
        return i;
    }

    public static void preprocessBM(String str, int[] lastPosition) {
        for (int i = 0; i < lastPosition.length; i++) {
            lastPosition[i] = -1;
        }
        for (int j = 0; j < str.length(); j++) {
            lastPosition[str.charAt(j)] = j;
        }
    }

    public static int foldString(String str) {
        int value = 0;
        for (int i = 0; i < str.length(); i++) {
            value += str.charAt(i);
        }
        return value;
    }

    public static void printFile(String filename, index ind) {
        System.out.println("File " + filename + " is being written");
        try {
            BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(filename), "UTF-8"));
            try {
                while (ind != null) {
                    bufferedWriter.write(ind.getValue());
                    bufferedWriter.newLine();
                    ind = ind.getNext();
                }
            } catch (IOException x) {
                x.printStackTrace();
            } finally {
                try {
                    bufferedWriter.close();
                } catch (IOException x) {
                    x.printStackTrace();
                }
            }
        } catch (IOException x) {
            x.printStackTrace();
        }
    }

    public static void printIndex(String filename, index[] ind) {
        System.out.println("File " + filename + " is being written");
        try {
            BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(filename), "UTF-8"));
            try {
                for (int i = 0; i < ind.length; i++) {
                    index t = ind[i];
                    if (t != null) {
                        bufferedWriter.newLine();
                        bufferedWriter.write(i + ":");
                    }
                    while (t != null) {
                        bufferedWriter.write(t.getValue() + "-" + t.getHash() + " ");
                        t = t.getNext();
                    }
                }
            } catch (IOException x) {
                x.printStackTrace();
            } finally {
                try {
                    bufferedWriter.close();
                } catch (IOException x) {
                    x.printStackTrace();
                }
            }
        } catch (IOException x) {
            x.printStackTrace();
        }
    }

    public static String capitalizeString(String string) {
        char[] chars = string.toLowerCase().toCharArray();
        boolean found = false;
        for (int i = 0; i < chars.length; i++) {
            if (!found && Character.isLetter(chars[i])) {
                chars[i] = Character.toUpperCase(chars[i]);
                found = true;
            } else if (Character.isWhitespace(chars[i]) || chars[i] == '.' || chars[i] == '\'') { // You can add other chars here
                found = false;
            }
        }
        return String.valueOf(chars);
    }

    /**
     *
     * @param array
     * @return
     */
    public static record[] removeDuplicates(record[] array) {
    // convert input array to populated list
    List<record> list=Arrays.asList(array);

    // convert list to populated set
    HashSet<record> set=new HashSet<>();
    set.addAll(list);

    // convert set to array & return, 
    // use cast because you can't create generic arrays
    return (record[]) set.toArray();
}
    
    public static void main(String[] args) {
        System.out.println("COMP 5511/2 DD, Fall 2017, group 10, assignment 4, task 1");
        String filename;
        if (args.length > 0) {
            filename = args[0];
        } else {
            filename = "ds17sAsg4data.txt";
        }
        System.out.println("Input file is " + filename);

        int numLines = numberLines(filename);
        if (numLines == -1) {
            System.out.println("Error reading file " + filename);
            System.exit(-1);
        }
        if (numLines == 0) {
            System.out.println("File " + filename + " is empty");
            System.exit(-2);
        }
        System.out.println("File " + filename + " has " + numLines + " lines");
        record[] recordData = new record[numLines];
        for (int i = 0; i < numLines; i++) {
            recordData[i] = new record();
        }
        readFile(filename, recordData);
        
        List<record> list=new ArrayList<record>();
        for(int i = 0; i<recordData.length;i++){
            
           list.add(recordData[i]);
        }

        // convert list to populated set
        HashSet<record> set=new HashSet<>();
        set.addAll(list);
        list.clear();
        list.addAll(set);
          
        recordData = new record[list.size()];
        for (int i = 0; i < list.size(); i++) {
            recordData[i] = new record();
            recordData[i] = list.get(i);
        }
            
        
        

        int indexCountryMax = 0, indexorganizationMax = 0;
        index indexCountryRoot = null, indexOrganizationRoot = null;

        for (int i = 0; i < recordData.length; i++) {
            String country = recordData[i].getCountry();
            String organization = recordData[i].getOrganization();
            int h = foldString(country);
            int org = foldString(organization);
            if (h > indexCountryMax) {
                indexCountryMax = h;
            }
            if (org > indexorganizationMax) {
                indexorganizationMax = org;
            }
            index newIndex = new index();
            newIndex.setIndex(country, h, i);
            newIndex.setNext(indexCountryRoot);
            indexCountryRoot = newIndex;

            index newIndex1 = new index();
            newIndex1.setIndex(organization, org, i);
            newIndex1.setNext(indexOrganizationRoot);
            indexOrganizationRoot = newIndex1;
        }

        System.out.println("Max hash value is " + indexCountryMax);
        System.out.println("Max hash value is " + indexorganizationMax);
        int indexCountryHash1 = 223;
        int indexCountryHash2 = 31;
        int indexOrganizationHash1 = 666;
        int indexOrganizationHash2 = 31;
        index[] indexCountry = new index[indexCountryHash1];
        index[] indexOrganization = new index[indexOrganizationHash1];

        for (int i = 0; i < indexCountryHash1; i++) {
            indexCountry[i] = null;
        }
        while (indexCountryRoot != null) {
            int h0 = indexCountryRoot.getHash() % indexCountryHash1;
            int h = indexCountryRoot.getHash() % indexCountryHash1;
            int h2 = indexCountryRoot.getHash() % indexCountryHash2 + 1;
            index t = indexCountryRoot.getNext();
            while (indexCountry[h] != null) {
                if (indexCountry[h].getValue().compareTo(indexCountryRoot.getValue()) == 0) {
                    break;
                }
                h = (h + h2) % indexCountryHash1;
                if (h0 == h) {
                    System.out.println("Error, not able to build hash array");
                    System.exit(-3);
                }
            }
            indexCountryRoot.setNext(indexCountry[h]);
            indexCountry[h] = indexCountryRoot;
            indexCountryRoot = t;
        }

        for (int i = 0; i < indexOrganizationHash1; i++) {
            indexOrganization[i] = null;
        }
        while (indexOrganizationRoot != null) {
            int h0 = indexOrganizationRoot.getHash() % indexOrganizationHash1;
            int h = indexOrganizationRoot.getHash() % indexOrganizationHash1;
            int h2 = indexOrganizationRoot.getHash() % indexOrganizationHash2 + 1;
            index t = indexOrganizationRoot.getNext();
            while (indexOrganization[h] != null) {
                if (indexOrganization[h].getValue().compareTo(indexOrganizationRoot.getValue()) == 0) {
                    break;
                }
                h = (h + h2) % indexOrganizationHash1;
                if (h0 == h) {
                    System.out.println("Error, not able to build hash array");
                    System.exit(-3);
                }
            }
            indexOrganizationRoot.setNext(indexOrganization[h]);
            indexOrganization[h] = indexOrganizationRoot;
            indexOrganizationRoot = t;
        }

        Scanner myscan = new Scanner(System.in);
        String field = "";
        do {
            System.out.println("Please select search by [o]rganization, [c]ountry ,[o,c]organization,country. Anything else - exit");
            String cmd = myscan.nextLine();
            field = "";
            if (cmd.compareTo("o") == 0) {
                field = "organization";
            } else if (cmd.compareTo("c") == 0) {
                field = "country";
            } else if (cmd.compareTo("o,c") == 0) {
                field = "OrganizationCountry";
            }

            if (!field.isEmpty()) {
                System.out.println("Please enter " + field);
                String value = myscan.nextLine();
                value = capitalizeString(value);
                if (field.compareTo("country") == 0) {
                    int r = 0;
                    int f = foldString(value);
                    int h0 = f % indexCountryHash1;
                    int h = f % indexCountryHash1;
                    int h2 = f % indexCountryHash2 + 1;
                    while (indexCountry[h] != null) {

                        if (indexCountry[h].getValue().compareTo(value) == 0) {
                            index t = indexCountry[h];
                            while (t != null) {
                                int e = t.getNumber();
                                System.out.println("Found " + field + "=" + value + ", Record=" + recordData[e].getAll());
                                t = t.getNext();
                                r++;
                            }
                            break;
                        } else {
                            for (int i = 0; i < recordData.length; i++) {
                                String org = recordData[i].getCountry();
                                if (org.matches(".*\\b" + value + "\\b.*")) {
                                    System.out.println("Found " + field + "=" + value + ", Record=" + recordData[i].fullName
                                            + ":" + recordData[i].email + ":" + recordData[i].organization + ":"
                                            + recordData[i].country);
                                    r++;
                                }

                            }
                        }
                        h = (h + h2) % indexCountryHash1;
                        if (h0 == h) {
                            System.out.println("Error, not able to build hash array");
                            System.exit(-3);
                        }
                    }
                    if (r == 0) {
                        System.out.println("No records found");
                    }

                } else if (field.compareTo("organization") == 0) {

                    int r = 0;
                    int f = foldString(value);
                    int h0 = f % indexOrganizationHash1;
                    int h = f % indexOrganizationHash1;
                    int h2 = f % indexOrganizationHash2 + 1;
                    while (indexOrganization[h] != null) {

                        if (indexOrganization[h].getValue().compareTo(value) == 0) {
                            index t = indexOrganization[h];
                            while (t != null) {
                                int e = t.getNumber();
                                System.out.println("Found " + field + "=" + value + ", Record=" + recordData[e].getAll());
                                t = t.getNext();
                                r++;
                            }
                            break;
                        } else {
                            for (int i = 0; i < recordData.length; i++) {
                                String org = recordData[i].getOrganization();
                                if (org.matches(".*\\b" + value + "\\b.*")) {
                                    System.out.println("Found " + field + "=" + value + ", Record=" + recordData[i].fullName
                                            + ":" + recordData[i].email + ":" + recordData[i].organization + ":"
                                            + recordData[i].country);
                                    r++;
                                }

                            }
                        }
                        h = (h + h2) % indexOrganizationHash1;
                        if (h0 == h) {
                            System.out.println("Error, not able to build hash array");
                            System.exit(-3);
                        }
                    }
                    if (r == 0) {
                        System.out.println("No records found");
                    }

                } else if (field.compareTo("OrganizationCountry") == 0) {
                    int r = 0;
                    String[] values = value.split(",");
                    values[0] = capitalizeString(values[0]);
                    values[1] = capitalizeString(values[1]);
                    int f_Country = foldString(values[1]);
                    int h0_Country = f_Country % indexCountryHash1;
                    int h_Country = f_Country % indexCountryHash1;
                    int h2_Country = f_Country % indexCountryHash2 + 1;
                    int f = foldString(values[0]);
                    int h0 = f % indexOrganizationHash1;
                    int h = f % indexOrganizationHash1;
                    int h2 = f % indexOrganizationHash2 + 1;
                    while (indexCountry[h_Country] != null) {
                        if (indexCountry[h_Country].getValue().compareTo(values[1]) == 0) {
                            while (indexOrganization[h] != null) {
                                if (indexOrganization[h].getValue().compareTo(values[0]) == 0) {
                                    index t = indexOrganization[h];
                                    while (t != null) {
                                        int e = t.getNumber();
                                        System.out.println("Found " + field + "=" + value + ", Record=" + recordData[e].getAll());
                                        t = t.getNext();
                                        r++;
                                    }
                                    break;
                                } else {
                                    for (int i = 0; i < recordData.length; i++) {
                                        String org = recordData[i].getOrganization();
                                        String country = recordData[i].getCountry();
                                        if (org.matches(".*\\b" + values[0] + "\\b.*") && country.compareTo(values[1]) == 0) {
                                            System.out.println("Found " + field + "=" + value + ", Record=" + recordData[i].fullName
                                                    + ":" + recordData[i].email + ":" + recordData[i].organization + ":"
                                                    + recordData[i].country);
                                            r++;
                                        }

                                    }
                                }
                                h = (h + h2) % indexOrganizationHash1;
                                if (h0 == h) {
                                    System.out.println("Error, not able to build hash array");
                                    System.exit(-3);
                                }
                            }
                            index t = indexCountry[h_Country];
                            break;
                        }
                        h_Country = (h_Country + h2_Country) % indexCountryHash1;
                        if (h0_Country == h_Country) {
                            System.out.println("Error, not able to build hash array");
                            System.exit(-3);
                        }
                        h = (h + h2) % indexOrganizationHash1;
                        if (h0 == h) {
                            System.out.println("Error, not able to build hash array");
                            System.exit(-3);
                        }
                    }
                    if (r == 0) {
                        System.out.println("No records found");
                    }
                }

            }
        } while (!field.isEmpty());
        myscan.close();
        System.out.println("Thank you for the search, have a great day!");
    }
}

