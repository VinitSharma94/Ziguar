# Ziguar
#Create an Apex class that implements the Database.Batchable interface to do the following in the org : - query all Lead records with Country = 'US'  - for queried records, update LeadSource = 'Web' and Rating = 'Hot'. Write unit tests that achieve 100% code coverage for the class.


#ApexBatchClass
global class UpdateLead implements Database.Batchable <SObject> {
//START METHOD
    global Database.QueryLocator start(Database.BatchableContext bc){
        String Query='Select id,LeadSource,Rating,Region__c from Lead where Region__c =  \'US\'';
        return Database.getQueryLocator(Query);
            }
//EXECUTE METHOD
    global void execute(Database.BatchableContext bc, List<Lead> scope){
        for(Lead l: scope){
            l.LeadSource='Web';
            l.Rating='Hot';
        }
        update scope;
    }
//FINISH METHOD
    global void finish(Database.BatchableContext bc){
        Id job= bc.getJobId();
        System.debug(job);
    }
}



#TestClass

@isTest
public class UpdateLeadTest {
    @testSetup
    static void setup() {
        List<Lead> Leads = new List<Lead>();
        for(Integer i=0; i <100; i++) {
            Leads.add(new lead(LastName = 'LastName'+i, FirstName='FirstName'+i, Email='test'+i+'@gmail.com', LeadSource='Web', Rating='Hot'));
        }
        insert Leads;
    }
    
    static testmethod void test() {
        Test.startTest();
        UpdateLead lp =new UpdateLead();
        Id BatchId = Database.executeBatch(lp);
        Test.stopTest();
        // after the testing stops, assert records were updated properly
        System.assertEquals([select count() from Lead Where LeadSource='Web'], 100);
    }
}
