const CURRENTUSERUNID = JSON.parse('@Html.Raw(Json.Serialize(Model.PK_UserUNID))');
    const OMUNID = $("#OMForm_OMUNID").val();
    const OMREQUESTFORM = $("#frmOM");
    const OMREQUESTFORMNAME = "frmOM";
    const ACTIONNAME = $("#OMForm_ActionName");
    const STATUS = $("#OMForm_OMStatus").val();
    const MODIFIEDDATE = $("#OMForm_ModifiedDate").val();
    const TotalSOTDocument = JSON.parse('@Html.Raw(Json.Serialize(Model.SOTDocumentCount))'); 
    let OMQCReqData = "@Html.Raw(Model.QCForm.QCRequirement)";
    const OMQCApprvStatus = "@Html.Raw(Model.QCForm.QCApprovalStatus)";
    //const QCPendingResult = JSON.parse('@Html.Raw(Json.Serialize(Model.QCPendingResult))');   
    //console.log(QCPendingResult);

    var FromPage = JSON.parse('@Html.Raw(Json.Serialize(Model.FromPage))');  

    const ActionID = "btnActionID";
    const OpportunitySource = "ddlSrcOpp";
    const BriefDesc = "txtOMDesc";
    const ClosingYear = "txtClosingYear";
    const ClosingMonth = "numClosingMth";
    const Prob = "numProbability";
    @*const ProbV2 = "ddlProbabilityV2";*@
    const DueDate = "dateDueDate";
    const InternalDueDate = "dateInternalDueDate";
    const BudgetRev = "txtBudgetRev";
    const BudgetGP = "txtBudgetGP";
    const BudgetGE = "txtBudgetGE";

    const BidMgmtBudget = "cashBudget";
    const BidMgmtCoreComponent = "ddlBidMgmtSolutionGroupUNID"; 

    const ContractPeriodRow = "rowContractPeriod";
    const ContractPeriodStartDate = "dateContractPStartDt";
    const ContractPeriodEndDate = "dateContractPEndDt";
    const ContractPeriodDuration = "numDuration";

    const CoreComponentBidMgmt = "ddlCoreComponent";

    const ProbOMStatus = JSON.parse('@Html.Raw(Json.Serialize(Model.PertinentInfoForm.OMStatus))'); 
    const ProbMinRange = JSON.parse('@Html.Raw(Json.Serialize(Model.MinRange))');  
    const ProbMaxRange = JSON.parse('@Html.Raw(Json.Serialize(Model.MaxRange))');  

    const SOTModal = "modalCreateSOT";
    const PopupMsgSOTNotesLink = "Draft SOT has been created in Notes SOT." + "<br>" + "To open SOT, please connect to VPN and logon to Notes client." + "<br>" + "Proceed?";

    const TableToReIndex = ["tableBUSolution", "ReadersTable", "AuthorsTable", "tableActivity", "tableLinkedOM"];
    const EnableTimer = true;

    let IsBUSolutionValidation = false;
    let BusinessUnitException = JSON.parse('@Html.Raw(Json.Serialize(Model.BUException))');
            
    $(document).ready(async function () {
        unlock_when_leave_page('OMForm_ActionName', 'OM', OMUNID, CURRENTUSERUNID);   
        
        var ArrayUIControl = JSON.parse('@Html.Raw(Json.Serialize(Model.UIControlLists))');
        var ButtonControlList = JSON.parse('@Html.Raw(Json.Serialize(Model.AccessControls))');
        
        let CustName = $('#' + CustomerSelectID + ' option:selected').text();
        $("#CustName").val(CustName);
                            
        if (JSON.parse('@Html.Raw(Json.Serialize(Model.PertinentInfoForm.FK_OMTypeUNID))') == 1.01) {
            $('#' + ContractPeriodRow).prop('hidden', false);
            ContractPeriodDateAllowValidation();
        }
        else {
            HideContractDates();
        }

        let PnCTableValidation = JSON.parse('@Html.Raw(Json.Serialize(Model.PertinentInfoForm.PnC))');       
        if (PnCTableValidation == true) {
            $("#ReadersTableTotalRow").rules("add", {
                CheckReadersTable: true, 
            });
            $("#AuthorsTableTotalRow").rules("add", {
                CheckAuthorsTable: true, 
            });          
        }
        else if (PnCTableValidation == false) {
            $("#ReadersTableTotalRow").rules("add", {
                CheckReadersTable: false, 
            });
            $("#AuthorsTableTotalRow").rules("add", {
                CheckAuthorsTable: false, 
            });
            
            //---Delete error message if visible
            $("#ReadersTableTotalRow-error").remove();
            $("#AuthorsTableTotalRow-error").remove();
        }

        $('#' + CoreComponentBidMgmt).select2ToTree();
        let CurrCoreComponentVal = $('#' + CoreComponentBidMgmt).val();
        if(CurrCoreComponentVal != null && CurrCoreComponentVal != ""){
            CoreComponentFunction(CurrCoreComponentVal);
            AssignValueToCoreComponent();
        }
        
        SetDuration();      
        MandatoryFields();    
        
        if ('@TempData["SuccessCreateSOT"]' == 'True') {
            NotesURL = JSON.parse('@Html.Raw(Json.Serialize(Model.NotesURL))');

            let ConfirmSOTLink = await ConfirmationSweetAlert.fire({
                title: "",
                confirmButtonText: 'Proceed',
                cancelButtonText: 'Cancel',
                html: PopupMsgSOTNotesLink,
            });

            if (ConfirmSOTLink.isDismissed == true) {
                return false;
            }

            window.location.href = NotesURL;
        }

        $('#conSecOtherInfo').collapse('hide');

        if (OMUNID == 0) {
            $('#conSecSalesOwnerInfo').collapse('show');
        } else {
            $('#conSecGeneralInfos').collapse('hide');
        }

      
        //---Form Check
        const myFormArray = ["frmOM"];
        ControlListWIthLockNFieldCheck(ArrayUIControl, true, ButtonControlList, OMUNID, "OM", CURRENTUSERUNID, $('#FlagSave').val(), EnableTimer, myFormArray);
        hideDisableButton("VwMainBody");




        @*Event Function Below*@

        $('#' + ContractPeriodStartDate).change(function() {
            let Duration = $('#' + ContractPeriodDuration).val();
            if (!Duration) {
                return;
            }

            SetContractEndDate(Duration);
            if ($("#frmOM").validate().element('#' + ContractPeriodEndDate) == true) {
                $('#' + ContractPeriodEndDate + "-error").remove();
            }
        });

        const initialIntDateValue = $('#' + InternalDueDate).val();
        let initialDate;

        if (initialIntDateValue) {
            initialDate = new Date(initialIntDateValue);
        }

        $('#' + InternalDueDate).change(function () {
            if (initialIntDateValue) {
                var selectedDate = new Date($(this).val());

                if (selectedDate < initialDate) {
                    $('#' + InternalDueDate).rules("add", {
                        CheckBackdateCustom: true
                    });
                } else {
                    $('#' + InternalDueDate).rules("add", {
                        CheckBackdateCustom: false
                    });
                }
            }
        });

        $('#' + ContractPeriodDuration).on("click keyup", function (event) {
            SetContractEndDate(this.value);
            if ($("#frmOM").validate().element('#' + ContractPeriodEndDate) == true) {
                $('#' + ContractPeriodEndDate + "-error").remove();
            }
        });

        $('#' + ClosingMonth + ', #' + ClosingYear).on("keyup", function() {
            if ($("#frmOM").validate().element('#' + ClosingMonth) == true) {
                $('#' + ClosingMonth + "-error").remove();
            }

            if ($("#frmOM").validate().element('#' + ClosingYear) == true) {
                $('#' + ClosingYear + "-error").remove();
            }
        });

        $("#frmOM").on("keydown", function(event) {

            if (event.target.tagName.toLowerCase() === 'textarea') {
                // Allow Enter key behavior inside the textarea
                return;
            }

            if (event.key === "Enter") {
                event.preventDefault(); // Prevent form submission
            }
        });

        $("#linkSOT").click(async function(event) {
            event.preventDefault();
            const PopupMsgSOTBtnLink = "To open SOT, please connect to VPN and log on to Notes client. Proceed?";
            const targetUrl = $(this).attr("href");
            const MultSOTList = JSON.parse('@Html.Raw(Json.Serialize(Model.MulitpletoSOT))');
            
            function MultipleSOTOpen()
            {
                $('#multLinkSotDataContainer').empty();

                for (let i = 0; i < MultSOTList.length; i++) {
                    const splitValues = MultSOTList[i].split("||");

                    const firstValue = splitValues[0];
                    const secondValue = splitValues[1];
                    const thirdValue = splitValues[2];

                    const link = $('<a>', {
                        text: firstValue + " - " + thirdValue,
                        href: secondValue,
                        style: "font-size: 12px;",
                        target: "_blank" // optional, opens the link in a new tab
                    });

                    const textPart = $('<span>', {
                        text: i + 1 + ". ",
                        style: "font-size: 12px;"
                    });

                    $('#multLinkSotDataContainer').append(
                        $('<div>', {
                            class: "row",
                            style: "margin-bottom: -5px;",
                            html: $('<div>', {
                                class: "col-md-12",
                                html: [textPart, link] // Array of elements to append
                            })
                        })
                    );
                }

                $('#modalMultLinkSOT').modal('show');
            }
            
            let ConfirmSOTLink = await ConfirmationSweetAlert.fire({
                title: "",
                confirmButtonText: 'Proceed',
                cancelButtonText: 'Cancel',
                html: PopupMsgSOTBtnLink,
            });

            if (ConfirmSOTLink.isDismissed == true) {
                return false;
            }
            else if (ConfirmSOTLink.isConfirmed == true) {

                let btnValue = "Close";
                let btnID = "btnClose";

                //---IF FORM CHANGES WERE DETECTED
                if (IsFieldChanged == true) {
                    let ConfirmFormChanges = await ChangedFormSweetAlert.fire({
                        html: '',
                        title: "Changes have yet to be saved. Do you want to proceed?",
                        confirmButtonText: 'Save and Proceed',
                        denyButtonText: 'Proceed without Save',
                    });

                    if (ConfirmFormChanges.isDismissed == true) {
                        //return false;
                    }

                    else if (ConfirmFormChanges.isDenied == true) {
                        $('#UpdateForm').val("No");

                        UnlockRecord_Common('OM', OMUNID, CURRENTUSERUNID);

                        if (MultSOTList && MultSOTList.length > 1) {
                            MultipleSOTOpen();
                        } else {
                            window.location.href = targetUrl;
                        }
                    }

                    else if (ConfirmFormChanges.isConfirmed == true) {

                        const urlsT = $(this).attr("href");

                        const fromPageInput = document.querySelector('input[name="FromPage"]');
                        fromPageInput.value = urlsT;

                        $('#UpdateForm').val("Yes");

                        SaveForm(btnValue, btnID);
                    }
                }
                //---ELSE LEAVE PAGE
                else {
                    UnlockRecord_Common('OM', OMUNID, CURRENTUSERUNID);

                    if (MultSOTList && MultSOTList.length > 1) {
                        MultipleSOTOpen();
                    } else {
                        window.location.href = targetUrl;
                    }
                }
            }
        });
    });
    
    function expandInfo() {
        var infoElement = document.getElementById("info");
        var isExpanded = infoElement.classList.contains("ellipsis");

        if (isExpanded) {
            infoElement.textContent = "@Model.PertinentInfoForm.OMDesc";
        } 

        infoElement.classList.toggle("ellipsis");
    }

    function ReIndexTable(TableList) {
        //---Loop through each table in array
        for (let x = 0; x <= TableList.length; x++) {
            let CurrentTable = TableList[x];
            let NewIndexNum = 0;
            WordingTable = `#${CurrentTable} tbody tr`;

            //---Loop through each row in table
            $(WordingTable).each(function () {
                let OldIndexNum = $(this).data("number");               

                //---Loop through each input in row
                $(this).find('input, select, textarea').each(function () {
                    //---No checkbox
                    if (this.name.indexOf(OldIndexNum) >= 0) {
                        var OldName = $(this).attr('name');

                        let NewName = OldName.replace('[' + OldIndexNum + ']', '[' + NewIndexNum + ']');
                        $(this).attr({ 'name': NewName });
                    }                  
                });

                NewIndexNum++;
            });
        }
    }

    function MultiTabValidationErrorFocus() {        
        var ele = $("#frmOM .input-validation-error"); 
        var tabToShow = ele.closest('.tab-pane');
        var getErroTabID = tabToShow.attr('id');
        var CheckParentTab = tabToShow.attr('Data-ParentidTab');
                   
        var ele12 = $(`#frmOM .input-validation-error`);
        //Check Active Tab
        var ref_this = $('#parentTab > li > .active').attr("id");
        var PreviousTabID = $('#parentTab > li > .active').attr("href");
        //if no more validation error on active tab, change to next tab which contain validation error
        if(CheckParentTab){
                $('.nav-tabs a[href="#' + tabToShow.attr('data-ParentTabCon') + '"]').tab('show');
                $('.nav-tabs a[href="#' + getErroTabID + '"]').tab('show');
                $('#' + ele12[0].id).focus();
        } else {
            $('.nav-tabs a[href="#' + getErroTabID + '"]').tab('show');
        }             
 
        $('a[data-toggle="tab"]').on('shown.bs.tab', function () {       
            $('#' + ele12[0].id).trigger("focus");
        });

        if (ele12[0].id == BUSolutionTotalRow) {
            IsBUSolutionValidation = true;
            //console.log(IsBUSolutionValidation);
            $("#tableBUSolution").trigger("focus");
        }
        else {
            $('#' + ele12[0].id).trigger("focus");
        }       
    }

    function MandatoryFields() {
        //---Mandatory fields
        $('#' + OpportunityType).rules("add", {
            required: true,
            messages: {
                required: 'This field is required.'
            }
        });

        $('#' + CustomerSelectID).rules("add", {
            required: true,
            messages: {
                required: 'This field is required.'
            }
        });

        $('#' + BriefDesc).rules("add", {
            required: true,
            CheckBriefDescMaxChar: true,
            CheckBriefDescCharType: true,
            messages: {
                required: 'This field is required.'
            }
        });

        $('#' + BUSolutionTotalRow).rules("add", {
            CheckBusinessUnitSolutionTableRow: true,
            CheckCoreComponentMandatory: true
        });

        //---Non Mandatory but specific format fields
        if (ProbMinRange != null && ProbMaxRange != null) {
            $('#' + Prob).rules("add", {
                CheckIsExceed: true,
            });
        }

        $('#' + ClosingYear).rules("add", {
            CheckYear: true,
        });

        $('#' + ClosingMonth).rules("add", {
            CheckMonth: true,
        });

        $('#' + InternalDueDate).rules("add", {
            CheckNoBackdateNonMandatory: true
        });
    }

    function EnablePertinentInfoSubmitValidation() {
        $('#' + OpportunitySource).rules("add", {
            required: true,
            messages: { required: 'This field is required.' }
        });

        $('#' + SalesRepSelectID).rules("add", {
            required: true,
            messages: { required: 'This field is required.' }
        });
       
        $('#' + ClosingYear).rules("add", {
            required: true,
            messages: { required: 'This field is required.' }
        });

        $('#' + ClosingMonth).rules("add", {
            required: true,
            messages: { required: 'This field is required.' }
        });

        $('#' + Prob).rules("add", {
            required: true,
            messages: { required: 'This field is required.' }
        });

        $('#' + BudgetRev).rules("add", {
            required: true,
            CheckMoreThanGP_Rev: true,
            messages: { required: 'This field is required.' }
        });

        $('#' + BudgetGP).rules("add", {
            required: true,
            CheckLessThanRev_GP: true,
            CheckMoreThanGE_GP: true,
            messages: { required: 'This field is required.' }
        });

        $('#' + BudgetGE).rules("add", {
            required: true,
            CheckLessThanRevGP_GE: true,
            messages: { required: 'This field is required.' }
        });

         $('#' + BUSolutionTotalRow).rules("add", {
            CheckBusinessUnitSolutionTableRow: true,
            CheckCoreComponentMandatory: true
        });
    }

    function DisablePertinentInfoSubmitValidation() {
        $('#' + OpportunitySource).rules("remove");
        $('#' + SalesRepSelectID).rules("remove");
        $('#' + ClosingYear).rules("remove", "required");
        $('#' + ClosingMonth).rules("remove", "required");
        $('#' + Prob).rules("remove", "required");
        $('#' + BudgetRev).rules("remove");
        $('#' + BudgetGP).rules("remove");
        $('#' + BudgetGE).rules("remove");

        $('#' + ClosingYear).rules("remove");
        $('#' + ClosingMonth).rules("remove");
    }

    function DisablePertinentInfoSubmitValidationForNew() {
        $('#' + OpportunitySource).rules("remove");
        $('#' + SalesRepSelectID).rules("remove");
        $('#' + ClosingYear).rules("remove", "required");
        $('#' + ClosingMonth).rules("remove", "required");
        $('#' + Prob).rules("remove", "required");
        $('#' + BudgetRev).rules("remove");
        $('#' + BudgetGP).rules("remove");
        $('#' + BudgetGE).rules("remove");

        $('#' + ClosingYear).rules("remove");
        $('#' + ClosingMonth).rules("remove");
        $('#' + BUSolutionTotalRow).rules("remove");
        $('#' + BriefDesc).rules("remove");
    }

    function DisablePertinentInfoForSave() {
        $('#' + OpportunitySource).rules("remove", "required");
        $('#' + SalesRepSelectID).rules("remove", "required");
        $('#' + Prob).rules("remove", "required");
        $('#' + BudgetRev).rules("remove", "required");
        $('#' + BudgetGP).rules("remove", "required");
        $('#' + BudgetGE).rules("remove", "required");

        @* $('#' + OpportunitySource).rules("remove");
        $('#' + SalesRepSelectID).rules("remove");
        $('#' + Prob).rules("remove", "required");
        $('#' + BudgetRev).rules("remove");
        $('#' + BudgetGP).rules("remove");
        $('#' + BudgetGE).rules("remove"); *@
    }

    function EnableBidMgmtValidation() {

        $('#' + BidMgmtBudget).rules("add", {
            required: true,
            messages: {
                required: 'This field is required.'
            }
        });

        $('#' + BidMgmtCoreComponent).rules("add", {
            required: true,
            messages: { required: 'This field is required.' }
        });     
    }

    @*Validation Functions*@

    function SetDuration() {
        let startDateValue = $('#' + ContractPeriodStartDate).val();
        let endDateValue = $('#' + ContractPeriodEndDate).val();

        if (!startDateValue && !endDateValue) {
            return;
        }

        var startDate = new Date(startDateValue);
        var endDate = new Date(endDateValue);

        //Calculate the difference in days
        var timeDifference = endDate - startDate;
        var daysDifference = timeDifference / (1000 * 60 * 60 * 24);

        // Convert days to months
        var monthsDifference = Math.floor(daysDifference / 30.44); // Average days in a month

        $('#' + ContractPeriodDuration).val(monthsDifference + 1);
    }

    function SetContractEndDate(SetDuration) {
        if (!SetDuration) {
            return;
        }

        let SetDurationValue = parseInt(SetDuration);
        let SetStartDate = $('#' + ContractPeriodStartDate).val();

        let modifiedDate = new Date(SetStartDate);
        modifiedDate.setMonth(modifiedDate.getMonth() + SetDurationValue);
        let modifiedDateValue = modifiedDate.toISOString().split('T')[0];
        //$('#' + ContractPeriodEndDate).val(modifiedDateValue);

        //--Subtract one day from modifiedDateValue
        let oneDayInMillis = 24 * 60 * 60 * 1000;
        let modifiedDateTimestamp = new Date(modifiedDateValue).getTime();
        let modifiedDateMinusOneDay = new Date(modifiedDateTimestamp - oneDayInMillis);
        let modifiedDateMinusOneDayValue = modifiedDateMinusOneDay.toISOString().split('T')[0];

        $('#' + ContractPeriodEndDate).val(modifiedDateMinusOneDayValue);
    }

    function ContractPeriodDateAllowValidation() {  
        $('#' + ContractPeriodStartDate).rules("add", {
            required: true,
            CheckNoBackdate: true,
            messages: { required: 'This field is required.' }
        });    
        
        $('#' + ContractPeriodEndDate).rules("add", {
            required: true,
            CheckNoBackdate: true,
            CheckStartEndDate: true,
            messages: { required: 'This field is required.' }
        });
    }

    function ContractPeriodDateCancelValidation() {                 
        $('#' + ContractPeriodStartDate).rules("remove");
        $('#' + ContractPeriodEndDate).rules("remove");      
    }

    function BriefDescMaxCharacterValidation(BriefDesc) {      
        let maxChar = 250; //---Maximum number of characters

        //---Check if the value exceeds the maximum number of characters
        if (BriefDesc.length > maxChar) {
            return false;
        }
        return true;
    }

    function BriefDescCharacterTypeValidation(BriefDesc) {      
        //let pattern = /^[^\n\r\s]+(\s[^\n\r\s]+)*$/; //---No spaces at the beginning or end, no return characters, and only one space between words
        let pattern = /^[^\n\r\s]+(\s[^\n\r\s]+)*.+$/; //---Included no empty strings

        //---Check if the value matches the pattern
        if (!pattern.test(BriefDesc)) {
            return false;
        }
        return true;
    }
    
    function CheckYear(SetYear) {
        var Today = new Date();
        var TodayYear = parseInt(Today.getFullYear());              
        var SetYearNum = parseInt(SetYear);
                
        if (STATUS == "New") {
            if (SetYearNum < TodayYear) {
                return false;
            }
            else {
                return true;
            }
        }
        //---If status is not new. Allow backdate
        return true;
    }

    function CheckMonth(SetMonth) {

        var Today = new Date();
        var TodayMonth = (Today.getMonth() + 1);
        var TodayYear = Today.getFullYear();  
        
        var SetYearNum = parseInt($('#' + ClosingYear).val());
        var SetMonthNum = parseInt(SetMonth);

        //---No error if SetMonth is null
        if (!SetMonth) {      
            return true;
        }

        if (STATUS != "New") {      
            return true;
        }

        if (SetYearNum > TodayYear) {
            return true;
        }

        if (SetYearNum < TodayYear) {
            return false; 
        }

        if (SetMonthNum >= TodayMonth) {
            return true;
        }

        return false;                  
    }

    function ProbabilityNotExceed(SetProb) {
        let MaxProb = JSON.parse('@Html.Raw(Json.Serialize(Model.MaxRange))'); 
        let MinProb = JSON.parse('@Html.Raw(Json.Serialize(Model.MinRange))');
       
        let SetProbNum = parseInt(SetProb);       

        if (SetProbNum < MinProb || SetProbNum > MaxProb) {
            return false;          
        } 
        else {
            return true;
        }
    }

    function CheckMoreThanGP(SetRev) {
        var SetGP = $('#' + BudgetGP).val();           
              
        let SetRevCalc = parseFloat(SetRev.replace(/,/g, ''));
        let SetGPCalc = parseFloat(SetGP.replace(/,/g, ''));

        if (!SetGPCalc) {
            return true;
        }

        if (SetRevCalc >= SetGPCalc) {
            //console.log("Rev is more than GP");
            return true;
        }
        else {
            //console.log("Rev is less than GP");
            return false;
        }
    }

    function CheckMoreThanGE(SetGP) {      
        var SetGE = $('#' + BudgetGE).val(); 

        let SetGPCalc = parseFloat(SetGP.replace(/,/g, ''));
        let SetGECalc = parseFloat(SetGE.replace(/,/g, ''));

        if (!SetGECalc) {
            return true;
        }
             
        if (SetGPCalc >= SetGECalc) {
            //console.log("GP is more than GE");
            return true;
        }
        else {
            //console.log("GP is less than GE");
            return false;
        }
    }

    function CheckLessThanRev(SetGP) {
        var SetRev = $('#' + BudgetRev).val();           
              
        let SetGPCalc = parseFloat(SetGP.replace(/,/g, ''));
        let SetRevCalc = parseFloat(SetRev.replace(/,/g, ''));

        if (!SetRevCalc) {
            return true;
        }

        if (SetGPCalc <= SetRevCalc) {
            //console.log("Rev is more than GP");
            return true;
        }
        else {
            //console.log("Rev is less than GP");
            return false;
        }
    }

    function CheckLessThanRevGP(SetGE) {   
        var SetRev = $('#' + BudgetRev).val(); 
        var SetGP = $('#' + BudgetGP).val(); 

        let SetRevCalc = parseFloat(SetRev.replace(/,/g, ''));
        let SetGPCalc = parseFloat(SetGP.replace(/,/g, ''));
        let SetGECalc = parseFloat(SetGE.replace(/,/g, ''));

        if (!SetGPCalc || !SetRevCalc) {
            return true;
        }
             
        if (SetGECalc <= SetGPCalc && SetGECalc <= SetRevCalc) {
            //console.log("GP is more than GE");
            return true;
        }
        else {
            //console.log("GP is less than GE");
            return false;
        }
    }

    function CheckBackdateCustomFunc() {

        return false;
    }

    function CheckBackdate(SetDate) {

        var Today = new Date();
        var TodayDate = Today.getFullYear() + '-' + (Today.getMonth() + 1) + '-' + Today.getDate();
        var TodayTime = Today.getHours() + ":" + Today.getMinutes() + ":" + Today.getSeconds();

        var SetDateTime = Date.parse(SetDate + ' ' + TodayTime);
        var TodayDateTime = Date.parse(TodayDate + ' ' + TodayTime);
       
        if (STATUS == "New") {
            if (SetDateTime < TodayDateTime) {
                return false;
            }
            else {
                return true;
            }
        }

        return true;
    }

    function CheckBackdateNonMandatory(SetDate) {
        //---No error if no value
        if (!SetDate) {            
            return true;
        }

        let Today = new Date();
        let TodayDate = Today.getFullYear() + '-' + (Today.getMonth() + 1) + '-' + Today.getDate();
        let TodayTime = Today.getHours() + ":" + Today.getMinutes() + ":" + Today.getSeconds();

        let SetDateTime = Date.parse(SetDate + ' ' + TodayTime);
        let TodayDateTime = Date.parse(TodayDate + ' ' + TodayTime);

        //---No backdate for New forms
        if (STATUS == "New" && SetDateTime < TodayDateTime) {
            return false;
        }

        return true;        
    }

    function CheckStartEndDate(SetDate) {
        var ComparedDate = $('#' + ContractPeriodStartDate).val();

        var Today = new Date();
        var TodayTime = Today.getHours() + ":" + Today.getMinutes() + ":" + Today.getSeconds();

        var EndDate = Date.parse(SetDate + ' ' + TodayTime);
        var StartDate = Date.parse(ComparedDate + ' ' + TodayTime);

        if (EndDate < StartDate) {
            return false;
        }
        else {
            return true;
        }
    }
  
    function CheckCoreComponentMandatory() {
        var Counter = 0;
        $('#tableBUSolution > tbody > tr').each(function () {
            var NID = GetNIDFromBUSTable(this.id);
            var CoreSolution = $(`#BUSolutionTable_${NID}__IsCore_Value`).val();
            //console.log(CoreSolution);
            if (CoreSolution == "true") {
                Counter++;
            }
        });

        if (Counter > 0) {
            return true;
        }
        else {
            return false;
        }    
    }

    function CountTableRows(Count) {
        if (Count == 0 || Count < 0) {
            return false;
        }
        else {
            return true;
        }
    }

    function GetAllQCCount(QCCount) {      
        if (QCCount < 10) {
            return false;
        }
        else {
            return true;
        }     
    }

    function GetNIDFromBUSTable(BUSolutionTableRowID) {
        //tableBUSolutionRow_
        return BUSolutionTableRowID.slice(19);
    }

    function GetNIDFromActivityTable(ActivityTableRowID) {
        //tableActivityRow_
        return ActivityTableRowID.slice(17);
    }

    function SetProbabilityErrorMsg() {
        if (ProbMinRange == ProbMaxRange) {
            return `OM status is ${ProbOMStatus}. Allowable probability should be ${ProbMinRange}%.`;
        }
        else {
            return `OM status is ${ProbOMStatus}. Allowable probability should be between ${ProbMinRange}% to ${ProbMaxRange}%.`;
        }        
    }

    jQuery.validator.addMethod("CheckNoBackdate", CheckBackdate, "This date is invalid");
    jQuery.validator.addMethod("CheckNoBackdateNonMandatory", CheckBackdateNonMandatory, "This date is invalid");
    jQuery.validator.addMethod("CheckBackdateCustom", CheckBackdateCustomFunc, "This date is invalid");
    jQuery.validator.addMethod("CheckStartEndDate", CheckStartEndDate, "This date is invalid");

    jQuery.validator.addMethod("CheckBriefDescMaxChar", BriefDescMaxCharacterValidation, "Text must not be more than 250 characters.");
    jQuery.validator.addMethod("CheckBriefDescCharType", BriefDescCharacterTypeValidation, "Text must in proper format and no return characters allowed.");

    jQuery.validator.addMethod("CheckYear", CheckYear, "Past closing year is not allowed.");
    jQuery.validator.addMethod("CheckMonth", CheckMonth, "Past closing month and year is not allowed.");
    jQuery.validator.addMethod("CheckMoreThanGP_Rev", CheckMoreThanGP, "Revenue should not be lesser than GP.");
    jQuery.validator.addMethod("CheckMoreThanGE_GP", CheckMoreThanGE, "GP should not be lesser than GE.");
    jQuery.validator.addMethod("CheckLessThanRev_GP", CheckLessThanRev, "GP should not be greater than Revenue.");
    jQuery.validator.addMethod("CheckLessThanRevGP_GE", CheckLessThanRevGP, "GE should not be greater than Revenue or GP.");

    jQuery.validator.addMethod("CheckBusinessUnitSolutionTableRow", CountTableRows, "Please specify the solution(s) required by the customer.");
    jQuery.validator.addMethod("CheckIsExceed", ProbabilityNotExceed, SetProbabilityErrorMsg());
    jQuery.validator.addMethod("CheckCoreComponentMandatory", CheckCoreComponentMandatory, "One core solution is required.");

    jQuery.validator.addMethod("CheckReadersTable", CountTableRows, "Please add at least one row.");
    jQuery.validator.addMethod("CheckAuthorsTable", CountTableRows, "Please add at least one row.");
